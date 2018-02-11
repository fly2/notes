# tf使用记录

### name_scope 及 variable_scope 的异同

name_scope及variable_scope的作用都是为了不传引用而访问跨代码区域变量的一种方式，其内部功能是在其代码块内显式创建的变量都会带上scope前缀（如上面例子中的a），这一点它们几乎一样。而它们的差别是，在其作用域中获取变量，它们对 tf.get_variable() 函数的作用是一个会自动添加前缀，一个不会添加前缀。

```python
#name_scope不自动添加前缀
with tf.name_scope("my_scope"):
    v1 = tf.get_variable("var1", [1], dtype=tf.float32)
    v2 = tf.Variable(1, name="var2", dtype=tf.float32)
    a = tf.add(v1, v2)

print(v1.name)  # var1:0
print(v2.name)  # my_scope/var2:0
print(a.name)   # my_scope/Add:0

#variable_scope添加前缀
with tf.variable_scope("my_scope"):
    v1 = tf.get_variable("var1", [1], dtype=tf.float32)
    v2 = tf.Variable(1, name="var2", dtype=tf.float32)
    a = tf.add(v1, v2)

print(v1.name)  # my_scope/var1:0
print(v2.name)  # my_scope/var2:0
print(a.name)   # my_scope/Add:0
```

### 模型保存

1. 创建saver时，可以指定需要存储的tensor，如果没有指定，则全部保存。
2. 创建saver时，可以指定保存的模型个数，利用max_to_keep=4，则最终会保存4个模型（下图中我保存了160、170、180、190step共4个模型）。
3. saver.save()函数里面可以设定global_step，说明是哪一步保存的模型。
4. 程序结束后，会生成四个文件：存储网络结构.meta、存储训练好的参数.data和.index、记录最新的模型checkpoint。

```python
# 首先定义saver类
saver = tf.train.Saver(max_to_keep=4)

# 定义会话
with tf.Session() as sess:

    sess.run(tf.global_variables_initializer())
    print "------------------------------------------------------"

    for epoch in range(300):
        if epoch % 10 == 0:
            print "------------------------------------------------------"
            # 保存模型
            saver.save(sess, "model/my-model", global_step=epoch)
            print "save the model"

        # 训练
        sess.run(train_step)
    print "------------------------------------------------------"
```

### 模型加载

1. 首先import_meta_graph，这里填的名字meta文件的名字。然后restore时，是检查checkpoint，所以只填到checkpoint所在的路径下即可，不需要填checkpoint，不然会报错“ValueError: Can’t load save_path when it is None.”。
2. 后面根据具体例子，介绍如何利用加载后的模型得到训练的结果，并进行预测。

```python
def load_model():
    with tf.Session() as sess:
        saver = tf.train.import_meta_graph('model/my-model-290.meta')
        saver.restore(sess, tf.train.latest_checkpoint("model/"))
```

