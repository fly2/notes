# Python使用问题记录

本文中使用为py3.5

### 条件运算

在条件运算时，&和|比>,<运算等级高，要注意加括号以保证结果正确。

### 输出中插入数字

以%开头，`.3f`表示小数点后保持三位。f表示为浮点型（float）。当有多个输出时，用`()`，输出的数字前用%表示开始。

```python
print('a is %.3f,b is %.3f'%(0.2,0.5))
-->a is 0.200,b is 0.500
```

### 空值

```python
None==None
-->True
#在df的列中得到空值得真值表
df.col1==None
#无法得到该列为空的真值表，需要使用isnull()
df.col1.isnull()

from numpy import NaN
NaN==NaN
-->False
```

### isinstance

```python
#通过内置函数isinstance进行判断变量是否为某个类型，是则返回True
isinstance(val,type)
a = 4
isinstance (a,int)
-->True
```

### if  not  列表

是判断列表是否为空，如果为空，则返回True，否则返回False

```python
if  not ['a','b','c']:
    print(1)
else:
    print(2)
-->2
```

### @的用法

'@'符号用作函数修饰符，即将被修饰的函数作为参数，返回修饰后的同名函数或其他可调用的东西。修饰符必须出现在函数定义前一行，不允许和函数定义在同一行。也就是说@A def f(): 是非法的。 只可以在模块或类定义层内对函数进行修饰，不允许修修饰一个类。

```python
#在addspam中先定义了new(*args)函数，在return中调用new函数，在new中调用fn函数
def addspam(fn):
    def new(*args):
        print("spam,spam,spam")
        return fn(*args)
    return new
@addspam
def useful(a,b):
    print(a**2+b**2)
#调用useful(4,3)时会将useful当作参数调用addspam(useful(4,3))
useful(4,3)
-->
spam,spam,spam
25
```

@lazy_property

在方法前加@property可以将方法直接当成属性访问。但会在每次执行c.area时都会调用一次，现在可以使用@lazy_property，使只在初始化时调用一次。

```python
class Circle(object): 
  def __init__(self, radius): 
    self.radius = radius 
  
  @property
  def area(self): 
    return 3.14 * self.radius ** 2
  
c = Circle(4) 
print(c.radius) 
print(c.area) 

def lazy_property(func):
    attr_name = "_lazy_" + func.__name__
 
    @property
    def _lazy_property(self):
        if not hasattr(self, attr_name):
            setattr(self, attr_name, func(self))
        return getattr(self, attr_name)
 
    return _lazy_property
```



### assert

断言，Python的assert是用来检查一个条件，如果它为真，就不做任何事。如果它为假，则会抛出AssertError并且包含错误信息。

```python
x=5
assert x > 0, "x is not zero or negative" 
assert x%2 == 0, "x is not an even number"


AssertionError                            Traceback (most recent call last)
<ipython-input-10-394dd573a39e> in <module>()
      1 x=5
      2 assert x > 0, "x is not zero or negative"
----> 3 assert x%2 == 0, "x is not an even number"

AssertionError: x is not an even number

```

### 多变量命名

使用locals()或者globals()，这两个函数提供了基于字典的访问局部和全局变量的方式。key为变量名，value为对应的变量值。

```python
for i in range(10):
    locals()['name%s' % i],c = i,10 
    #通过字典的方式生成命名为name1，name2，。。。，name9的变量
```

### 函数的参数

python函数参数定义有四种方法：位置参数，默认参数，可变参数，关键字参数，四种如果要同时使用多种参数，需要按照（位置参数，默认参数，可变参数，关键字参数）顺序定义。

```python
#位置参数
def fun(a,b):
    print(a)
    print(b)
fun(20,30)
->20
->30

#默认参数
def fun(a=10,b=20):
    print(a)
    print(b)
fun()
->10
->20

#可变参数定义需加一个*
#可变参数会将多的参数转变为list，放入的可变参数中
def fun(a,b,*c):
    print(a)
    print(b)
    for i in c:
        print(i)
    print(c)
#c取到的为a，b多余的元素，如果为列表[3,4]，则c中只有一个元素，即列表[3,4]，而不是当前这样，c为一个列表，里面有3,4两个元素
fun(10,20,3,4)
->10
->20
->3
->4
->(3, 4, 5, 6, 7)

#关键字参数，定义需加**
#关键字参数会将参数形成dict对
def fun(a,**kw):
    print(a)
    print(type(kw))
    for i in kw.keys():
        print(i,'  ',kw[i])
fun(10,s=2,c=3)
->10
-><class 'dict'>
->c    3
->s    2
```

### 多函数使用

对于多函数直接用字典或列表循环调用即可。

```python
def a():
    print('a')
def b():
    print('b')
c=[a,b]
for i in c:
    i()
->a
->b
```

### 小数取整

```python
#向下取整
#向下取整直接用内建的 int() 函数即可：
>>> a = 3.75
>>> int(a)
3
#使用math的floor方法：
>>>math.floor(a)
3

#四舍五入
#对数字进行四舍五入用 round() 函数：
>>> round(3.25); round(4.85)
3.0
5.0

#向上取整
#向上取整需要用到 math 模块中的 ceil() 方法:
>>> import math
>>> math.ceil(3.25)
4.0
```

### yield使用

用于函数中，将函数变成生成器。保存的是函数的状态，每次在当前状态上迭代。

```python
def feb(max):
    n, a, b = 0, 0, 1
    while n<max:
        yield b
        a, b = b, a + b
        n = n + 1
for i in feb(10):
    print(i)
```

## 文件存取

### 文件读取

```python
读取txt
#方法一
f=open('test.txt')        
#返回文件对象
line=f.readline()
#读取一行文件
while line:
    print(line)
    line=f.readline()
f.close()
#方法二
line=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/stopword.txt').readlines()] 
#readlines()为读取全部行的数据
#line.strip()为去除每行后面的'\n'
```

```python
读取csv
#按行读取
f=open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/product.txt',encoding="gb18030").readlines()
#按照分隔符进行分割
line=[line.split(',') for line in f]
```



### 文件保存

```python
#保存为txt,在win显示需加\r
with open ('/home/weblogic/DATA/private/shangguanxf/txt_anaysis/create/product_p.txt','wt') as file2:
    for key in product_p:
        file2.writelines('%s,%s\n' % (key, product_p[key]))    
        
#保存为csv
with codecs.open(os.path.join("/home/weblogic/DATA/private/shangguanxf/cc_bigdata/create/工单机构分布/画图/",filespath),"w",'gb18030') as datacsv:
            #读取csv文件，返回的是迭代类型
            c1 = csv.writer(datacsv,dialect = ("excel"))
            for one in out.items():
                c1.writerow([one[0],one[1]])
            for two in tmp.items():
                c1.writerow([two[0],two[1]])
```

### 文件追加

```python
#在原有文件后面追加，即需要设置模式为'a'
with open('D:/Program Files (x86)/Tencent/WeChat/WeChat Files/s_g_x_f/Files/data2007.csv','a') as f:
        for i in city:
            f.writelines('%s,%s,%s,%s\n' % (i,data[i][0],data[i][1],data[i][2]))
```



## json读写

```python
import json
#生成json数据
data = {
'name' : 'ACME',
'shares' : 100,
'price' : 542.23
}
 
json_str = json.dumps(data)
#将字符串转回python数据结构
data1=json.loads(json_str)

#写json文件
with open('data.json', 'w') as f:
    json.dump(data,f)
#读json文件
with open('data.json', 'r') as f:
    data = json.load(f)
```

## xml读写

xml格式简单介绍

```xml
<?xml version="1.0" ?> 
- <data>
- <country name="Singapore">#name为country的属性，country为标签(tag)
  <rank>4</rank> #4为'rank'标签的text
  <year>2011</year> 
  <gdppc>59900</gdppc> 
  <neighbor name="Malaysia" direction="N" /> 
  </country>
- <country name="Panama">
  <rank>68</rank> 
  <year>2011</year> 
  <gdppc>13600</gdppc> 
  <neighbor name="Costa Rica" direction="W" /> 
  <neighbor name="Colombia" direction="E" /> 
  </country>
  </data>
```



xml读取有三种方法SAX，DOM，以及ElementTree。因DOM需要将XML数据映射到内存中的树，一是比较慢，二是比较耗内存，而SAX流式读取XML文件，比较快，占用内存少，但需要用户实现回调函数（handler）。ElementTree相对DOM速度要快，api接口也相对友善，因此这里只介绍ElementTree。

**注意：**

xml.etree.ElementTree模块不能防止恶意构造的数据。 如果您需要解析不受信任或未经身份验证的数据，请参阅[XML漏洞](https://docs.python.org/3.5/library/xml.html#xml-vulnerabilities)。

```python
import xml.etree.ElementTree as ET
#读取xml文件
tree = ET.parse('country_data.xml')
root = tree.getroot()
#读取xml格式的文本
root = ET.fromstring(country_data_as_string)
#使用下标访问
root[0][1].text
-->'2011'
#一级下标为country,二级下标为country中包含的标签。此例中[0][1]指第一个country,中的year，text为该标签的内容

#for循环遍历输出
for child in root: 
    #到country一级
    print(child.tag, "---", child.attrib) 
    for i in child:
        #到rank，year...这一级
        print(i.text)
        #输出文本，由于neighbor没有text会输出None

#使用iter直接遍历二级标签
for neighbor in root.iter('neighbor'):
...     print(neighbor.attrib)
#for neighbor in root.iter('yaer'):
#...     print(neighbor.text)

#通过搜索标签来输出，当为一级标签时直接搜索标签，当为二级标签时，搜索为路径'country/year'
for i in root.findall('country/year'):
    #find为搜索到的第一个元素，findall为全部元素
    print(i.text)

#写出xml文件
tree.write(out_path, encoding="utf-8",xml_declaration=True,method="xml")
#out_path 输出地址
#encoding 编码，默认为"us-ascii"
#xml_declaration 是否将xml声明添加到文件中，默认为None。True为始终添加，False为从添加，None为不是这三种编码时添加（US-ASCII or UTF-8 or Unicode）
#method设置输出格式，可选'xml','html','txt',默认为'xml'
```

## str

在字符串前面加u，`u'中国'`表示unicode编码，可以直接使用encode进行编码转换。字符串前面加b，`b'\xe4\xb8\xad\xe5\x9b\xbd'`表示byte字符串，可直接根据编码格式进行解码。

### 转义字符

| 转义字符    | 描述            | 转义字符   | 描述                        |
| ------- | ------------- | ------ | ------------------------- |
| \(在行尾时) | 续行符           | \other | 其它的字符以普通格式输出              |
| \\      | 反斜杠符号         | \xyy   | 十六进制数，yy代表的字符，例如：\x0a代表换行 |
| \'      | 单引号           | \oyy   | 八进制数，yy代表的字符，例如：\o12代表换行  |
| \"      | 双引号           | \f     | 换页                        |
| \a      | 响铃            | \r     | 回车                        |
| \b      | 退格(Backspace) | \t     | 横向制表符                     |
| \e      | 转义            | \v     | 纵向制表符                     |
| \000    | 空             | \n     | 换行                        |

### 字符串运算

下列实例中a='Hello',b='Python'

| 操作符    | 描述                                       | 实例                   |
| ------ | ---------------------------------------- | :------------------- |
| +      | 字符串连接                                    | a+b>>>'HelloPython'  |
| *      | 重复输出字符串                                  | a * 2>>>'HelloHello' |
| []     | 通过索引获取字符串中字符                             | a[1]>>>'e'           |
| [ : ]  | 截取字符串中的一部分                               | a[1:4]>>>'ell'       |
| in     | 成员运算符 - 如果字符串中包含给定的字符返回 True             | "H" in a>>>True      |
| not in | 成员运算符 - 如果字符串中不包含给定的字符返回 True            | "M" not in a>>>True  |
| r/R    | 原始字符串 - 原始字符串：所有的字符串都是直接按照字面的意思来使用，不会进行转义字符操作。 原始字符串除在字符串的第一个引号前加上字母"r"（可以大小写）以外，与普通字符串有着几乎完全相同的语法。 | print(r'\n')>>>  \n  |

```python
#r操作符实例
print('a\nb')
-->
a
b
print(r'a\nb')
-->
a\nb
```

### 字符串格式化

**注意：**格式化操作符辅助指令写在字符串格式化符号前面。

```python
#示例
#保留两位小数的浮点数
print("My name is %s and weight is %.2f kg!" % ('Zara', 98.2) )
-->My name is Zara and weight is 98.20 kg!
```

字符串格式化符号。

| 符   号 | 描述                 |
| ----- | ------------------ |
| %c    | 格式化字符及其ASCII码      |
| %s    | 格式化字符串             |
| %d    | 格式化整数              |
| %u    | 格式化无符号整型           |
| %o    | 格式化无符号八进制数         |
| %x    | 格式化无符号十六进制数        |
| %X    | 格式化无符号十六进制数（大写）    |
| %f    | 格式化浮点数字，可指定小数点后的精度 |
| %e    | 用科学计数法格式化浮点数       |
| %E    | 作用同%e，用科学计数法格式化浮点数 |
| %g    | %f和%e的简写           |
| %G    | %f 和 %E 的简写        |
| %p    | 用十六进制数格式化变量的地址     |

格式化操作符辅助指令：

| 符号    | 功能                                       |
| ----- | ---------------------------------------- |
| *     | 定义宽度或者小数点精度                              |
| -     | 用做左对齐                                    |
| +     | 在正数前面显示加号( + )                           |
| <sp>  | 在正数前面显示空格                                |
| #     | 在八进制数前面显示零('0')，在十六进制前面显示'0x'或者'0X'(取决于用的是'x'还是'X') |
| 0     | 显示的数字前面填充'0'而不是默认的空格                     |
| %     | '%%'输出一个单一的'%'                           |
| (var) | 映射变量(字典参数)                               |
| m.n.  | m 是显示的最小总宽度,n 是小数点后的位数(如果可用的话)           |

### 内置函数

#### str.split()

字符串分割

```python
#以 str 为分隔符切片 string，如果 num有指定值，则仅分隔 num 个子字符串
string.split(str,num)
#string为字符串
#str为分割的字符串
#num为分割次数，默认为全部分割

string='君不见，黄河之水天上来，奔流到海不复回。君不见，高堂明镜悲白发，朝如青丝暮成雪'
string.split('，')
-->['君不见', '黄河之水天上来', '奔流到海不复回。君不见', '高堂明镜悲白发', '朝如青丝暮成雪']
```

#### str.join()

字符串合并

```python
#以 string 作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
string.join(seq)
#seq为待合并的字符串list

s=['君不见', '黄河之水天上来', '奔流到海不复回。君不见', '高堂明镜悲白发', '朝如青丝暮成雪']
'/'.join(s)
-->'君不见/黄河之水天上来/奔流到海不复回。君不见/高堂明镜悲白发/朝如青丝暮成雪'
```

#### str.replace()

```python
#把 string 中的 str1 替换成 str2,如果 num 指定，则替换不超过 num 次.
string.replace(str1,str2,num)
#str1为匹配的字符串
#str2为替换后的字符串
#num为替换次数，默认为全部

string='君不见，黄河之水天上来，奔流到海不复回。君不见，高堂明镜悲白发，朝如青丝暮成雪'
string.replace('君','你')
-->'你不见，黄河之水天上来，奔流到海不复回。你不见，高堂明镜悲白发，朝如青丝暮成雪'
```

#### str.strip()

```python
#删除字符串前后的空格
string.strip()

string='                hello world!          '
string.strip()
-->'hello world!'

string.lstrip()#删除字符串左边的空格
string.rstrip()#删除字符串右边的空格
```

#### str.count()

```python
#返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数
string.count(str,beg,end)
#str 需要统计出现次数的字符串
#beg 统计的开始位置
#end 统计的结束位置

string='君不见，黄河之水天上来，奔流到海不复回。君不见，高堂明镜悲白发，朝如青丝暮成雪'
string.count('见',0,3)
-->1 
#因为只统计string[0:3]中'见'的次数，故为1次
```

#### string.encode()和string.decode()

```python
#以 encoding 指定的编码格式解码 string，如果出错默认报一个 ValueError 的 异 常 ， 除 非 errors 指 定 的 是 'ignore' 或 者'replace'
string.decode(encoding='UTF-8', errors='strict')

#以 encoding 指定的编码格式编码 string，如果出错默认报一个ValueError 的异常，除非 errors 指定的是'ignore'或者'replace'
string.encode(encoding='UTF-8', errors='strict')
#encoding 编码格式
#errors 错误处理方式
常见编码格式'gb18030','gb2312','utf-8','gbk'

```

#### str.find

**注意：** str.find只会返回找到的第一个匹配的字符的开始的位置，后面匹配到的将没有任何返回

```python
#检测 str 是否包含在 string 中，如果 beg 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1
string.find(str, beg=0, end=len(string))
#str 要寻找的字符串
#beg 开始搜索位置，默认为0
#end 结束搜索位置，默认为len(string)

string='君不见，黄河之水天上来，奔流到海不复回。君不见，高堂明镜悲白发，朝如青丝暮成雪'
string.find('见')
-->2 #只返回了第一个匹配的'见'的其实位置

```

#### str.maketrans和str.translate

```python
#根据 str 给出的表(包含 256 个字符)转换 string 的字符,要过滤掉的字符放到 del 参数中
string.translate(str, del="")
#str为由maketrans生成的替换表
#del为要过滤掉的字符

#maketrans() 方法用于创建字符映射的转换表，对于接受两个参数的最简单的调用方式，第一个参数是字符串，表示需要转换的字符，第二个参数也是字符串表示转换的目标。
string.maketrans(intab, outtab)
#intab 需要转换的字符
#outtab 映射后的字符


string = "this is string example....wow!!!"
trantab=str.maketrans('aeiou', "12345")
string.translate(trantab)
-->'th3s 3s str3ng 2x1mpl2....w4w!!!'
```

## os

### 得到当前路径

```python
os.getcwd()
->'E:\\code\\python'
```

### 获取指定目录下的所有文件和目录名

```python
os.listdir('dirname')
#返回一个列表
```

### 检测文件/文件夹是否存在

```python
os.path.exists(path)
#path可以为当前路径下的文件，也可以是绝对路径
os.path.exists('gensim使用记录.md')
->True
os.path.exists('d:/1111.txt')
->True
```

### 生成文件夹

```python
os.mkdir('d:/tmp1')
#生成文件夹

os.makedirs('d:/tmp2/tmp1')
#生成多层文件夹（即生成tmp2，tmp1）
```

### 删除文件夹

```python
os.rmdir(path)
#只能删除空文件夹
os.rmdir('d:/tmp1')

os.removedirs(path)
#删除子目录后如果子目录为空则会同时删除父目录
```

### 删除文件

```python
os.remove(path)
#删除指定文件
os.remove('d:/1111.txt')
```

## shutil

### 删除目录以及目录内的所有内容

```python
shutil.rmtree('d:/test')
```

## multiprocessing

得到电脑的cpu核心数

```python
import multiprocessing
workers=multiprocessing.cpu_count()
```

## collections

### deque

deque是为了高效实现插入和删除操作的双向列表，适合用于队列和栈。`deque`除了实现list的`append()`和`pop()`外，还支持`appendleft()`和`popleft()`，这样就可以非常高效地往头部添加或删除元素。deque还可以定义最大长度，当有超过最大长度的元素插入时，会根据插入方向append，appendleft反向删除元素。

```python
from collections import deque
q = deque(['a', 'b', 'c'])
q.append('x')
q.appendleft('y')
q
deque(['y', 'a', 'b', 'c', 'x'])

q=deque(maxlen=5)
for i in range(10):
    q.appendleft(i)
print(q)
->deque([9, 8, 7, 6, 5], maxlen=5)

q=deque(maxlen=5)
for i in range(10):
    q.append(i)
print(q)
->deque([5, 6, 7, 8, 9], maxlen=5)
```

### Counter

计数器的更新包括增加和减少两种。其中，增加使用update()方法，减少则使用subtract()方法。Counter()内可以使用list

```python
from collections import Counter
c = Counter()
for ch in 'programming':
	c[ch] = c[ch] + 1

c
->Counter({'g': 2, 'm': 2, 'r': 2, 'a': 1, 'i': 1, 'o': 1, 'n': 1, 'p': 1})
s='门特挂号;糖尿病,糖尿病合并微循环病变,糖尿病合并冠心病;门特挂号;偏瘫,脑梗死 高血压病'
c = Counter(re.split(':|,|、| |;',s))

c = Counter('which')
c.update('witch')  # 使用另一个iterable对象更新
c['h']
->3
d = Counter('watch')
c.update(d)  # 使用另一个Counter对象更新
c['h']
->4


c = Counter('which')
c.subtract('witch')  # 使用另一个iterable对象更新
c['h']
->1
d = Counter('watch')
c.subtract(d)  # 使用另一个Counter对象更新
c['a']
->-1
```



## io

### 代替文件进行操作（io.StringIO）

使用StringIO可以省去建立文件的步骤，直接模拟读取数据

```python
from io import StringIO
data = 'a,b,c\n1,2,3\n4,5,6,7\n8,9,10'
pd.read_csv(StringIO(data), error_bad_lines=False)
```

## numpy

numpy生成向量时，不要使用[1,n]/(1,n)这种形式，直接用单个n进行赋值，否则会生成矩阵，不能直接取值，需要array[0]才能取到值

### np的bool索引赋值

在对np生成的数组以bool索引赋值时，bool需要为np.array类型。

```python
true=alltime2.loc[:,tlist1].sum(axis=1)>0
buy=np.zeros(len(alltime2),'int8')
buy[np.array(true)]=1
```

### 得到数组的频数

将数组内的数当成索引，得到各索引的出现次数。

```python
# 我们可以看到x中最大的数为7，因此bin的数量为8，那么它的索引值为0->7
x = np.array([0, 1, 1, 3, 2, 1, 7])
# 索引0出现了1次，索引1出现了3次......索引5出现了0次......
np.bincount(x)
#因此，输出结果为：array([1, 3, 1, 1, 0, 0, 0, 1])

# 我们可以看到x中最大的数为7，因此bin的数量为8，那么它的索引值为0->7
x = np.array([7, 6, 2, 1, 4])
# 索引0出现了0次，索引1出现了1次......索引5出现了0次......
np.bincount(x)
#输出结果为：array([0, 1, 1, 0, 1, 0, 1, 1])
```

### 生成随机整数

```python
import numpy as np
np.random.randint(a, b, size=(c, d))
#生成范围在[a,b)的整数
#size为生成数组行列
```

### 生成0

```python
#生成5维的0向量
np.zeros((5))
#生成(3,5)的0矩阵
np.zeros((3,5))
```



## time

### 计算时间

可以通过`time.time()`来得到当前的时间戳（1970年起始），两个时间戳相减，便得到运行时间

```python
import time

time1=time.time()
print(time.time()-time1)
```

### 暂停程序

可以通过`time.sleep()`来使程序暂停。

```python
import time
time.sleep(1)
#程序暂停1s，实际暂停时间可能比1s短
```

### 当前时间

```python
t=time.strftime('%Y%m%d',time.localtime(time.time()))
#得到当前时间，以年月日格式返回。
```



