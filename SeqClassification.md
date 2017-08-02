## SeqClassification

```python
import tensorflow as tf
from tensorflow.models.rnn import rnn
from tensorflow.models.rnn import rnn_cell

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

