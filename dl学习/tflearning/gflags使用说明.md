**gflags使用说明：**

1.gflags.DEFINE_type可以定义输入参数，这里列举了常用的boolean，integer，string，参数的含义分别为定义名称，默认值和该参数的说明，例如例子中的name可以使用--name去赋值；

2.直接在运行的时候使用--help可以看到所有的输入参数的默认值和说明；

3.gflags.FLAGS(argv)对参数进行初始化处理；

4.调用的时候直接使用gflags.FLAGS.name去调用；

5.代码中的FLAGS=gflags.FLAGS相当于别名。

```python
import sys  
import gflags  
import logging  
Flags = gflags.FLAGS  
gflags.DEFINE_string('name', 'func_test', 'test function name')  
gflags.DEFINE_integer('qps', 0, 'test qps')  
gflags.DEFINE_boolean('debug', False, 'whether debug')  
def main(argv):  
    Flags(argv)  
    logging.basicConfig(level=logging.DEBUG,  
                format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',  
                datefmt='%a, %d %b %Y %H:%M:%S',  
                filename='test.log',  
                filemode='w')  
    logging.debug(Flags.name)  
    logging.info(Flags.qps)  
    logging.warning(Flags.debug)  
if __name__ == "__main__":  
    main(sys.argv)  
```

