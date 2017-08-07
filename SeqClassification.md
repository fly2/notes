## SeqClassification

```python
import tensorflow as tf
from tensorflow.contrib.rnn.python.ops import rnn
from tensorflow.contrib.rnn.python.ops import rnn_cell
from tensorflow.contrib.rnn import GRUCell
import argparse

from attrdict import AttrDict

class SequenceClassificationModel:
    def __init__(self,data,target,params):
        self.data=data
        self.target=target
        self.params=params
        self.prediction
        self.cost
        self.error
        self.optimize
     def lazy_property(func):
    	attr_name = "_lazy_" + func.__name__

    	@property
    	def _lazy_property(self):
        	if not hasattr(self, attr_name):
            	setattr(self, attr_name, func(self))
        	return getattr(self, attr_name)

    	return _lazy_property
    
    @lazy_property
    def length(self):
        #取每行最大值，大于0为1
        used=tf.sign(tf.reduce_max(tf.abs(self.data),reduction_indices=2))
        #按列求和，得到长度
        length=tf.reduce_sum(used,reduction_indices=1)
        length=tf.cast(length,tf.int32)
        return length
    @lazy_property
    def prediction(self):
        #循环神经网络
        output,_=rnn.dynamic_rnn(
            self.params.rnn_cell(self.params.rnn_hidden),
            self.data,
            dtype=tf.float32,
            sequence_length=self.length,
                                )
        last=self._last_relevant(output,self.length)
        #softmax层
        num_classes=int(self.target.get_shape()[1])
        weight=tf.Variable(tf.truncated_normal(
                [self.params.rnn_hidden,num_classes],stddev=0.01))
        bias=tf.Variable(tf.constant(0.1,shape=[num_classes]))
        prediction=tf.nn.softmax(tf.matmul(last,weight)+bias)
        return prediction
    @lazy_property
    def cost(self):
        #交叉熵本来是对分类的概率进行计算，此处为对每行的数据的onehot标签与分类概率相乘，每行可能有两种情况0*log(y[i]),1*log(y[i]),通过计算可以得到实际分类的一个信息熵，最后通过tf.reduce_sum对所有行列求和
        cross_entropy=-tf.reduce_sum(self.target*tf.log(self.prediction))
        return cross_entropy
    @lazy_property
    def optimize(self):
        gradient=self.params.optimizer.compute_gradients(self.cost)
        if self.params.gradient_clipping:
            limit=self.params.gradient_clipping
            gradient=[
              	(tf.clip_by_value(g,-limit,limit),v)
                if g is not None else (None,v)
                for g,v in gradient]
        optimize=self.params.optimizer.apply_gradients(gradient)
        return optimize
    @lazy_property
    def error(self):
        mistakes=tf.not_equal(
            tf.argmax(self.target,1),
            tf.argmax(self.prediction)
                             )
        return tf.reduce_mean(tf.cast(mistakes,tf.float32))
    @staticmethod
    def _last_relevant(output,length):
        batch_size=tf.shape(output)[0]
        max_length=int(output.get_shape()[1])
        output_size=int(output.get_shape()[2])
        index=tf.range(0,batch_size)*max_length+(length-1)
        flat=tf.reshape(output,[-1,output_size])
        relevant=tf.gather(flat,index)
        return relevant
```

```python
#处理数据进行对齐
def preprocess_batched(iterator,length,embedding,batch_size):
    iterator=iter(iterator)
    while True:
        data=np.zeros((batch_size,length,embedding.dimensions))
        target=np.zeros(batch_size,2)
        for index in range(batch_size):
            text,label=next(iterator)
            data[index]=embedding(text)
            #label=1则target为[1,0],否则为[0,1]
            target[index]=[1,0]if label else [0,1]
            yield data,target
      
```

```python
params=AttrDict(
    rnn_cell=GRUCell,
    rnn_hidden=300,
    optimizer=tf.train.RMSPropOptimizer(0.002),
    batch_size=20,
)

reviews=ImbdMovieReviews('/home/user/imdb')
length=max(len(x[0])for  x in reviews)

embedding=Embedding(
'/home/user/wikipedia/vocabulary.bz2',
'/home/user/wikipedia/embedding.npy'length
)
batches=preprocess_batched(reviews,length,embedding,params.batch_size)
data=tf.placeholder(tf.float32,[None,length,embedding.dimensions])
target=tf.placeholder(tf.float32,[None,2])
model=SequenceClassificationModel(data,target,params)

sess=tf.Session()
sess.run(tf.global_variables_initializer())
for index,batch in enumerate(batches):
    feed={data:batch[0],target:batch[1]}
    error,_=sess.run([model.error,model.optimize],feed)
    print('{}:{:3.1f}%'.format(index+1,100*error))
```

