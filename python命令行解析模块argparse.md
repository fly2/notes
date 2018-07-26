# [python命令行解析模块argparse](https://blog.csdn.net/guoyajie1990/article/details/76739977)

## **一、argparse模块**

python标准库模块argparse用于解析命令行参数，编写用户友好的命令行界面，该模块还会自动生成帮助信息，并在所给参数无效时报错。 
首先看一个例子：

```python
#arg_parse.py
#coding:utf-8
import argparse

parser = argparse.ArgumentParser(description='Process some integers.')
parser.add_argument('integers', metavar='N', type=int, nargs='+',help='an integer for the accumulator')
parser.add_argument('--sum', dest='accumulate', action='store_const',const=sum, default=max,
                      help='sum the integers (default: find the max)')
args = parser.parse_args()
print(args.accumulate(args.integers))12345678910
```

将上述代码保存为arg_parse.py，在命令行运行该脚本。使用-h选项可以查看帮助信息

```
$ python prog.py -h
usage: prog.py [-h] [--sum] N [N ...]
Process some integers.
positional arguments:
 N           an integer for the accumulator
optional arguments:
 -h, --help  show this help message and exit
 --sum       sum the integers (default: find the max)12345678
```

如果不指定–sum选项，则找出输入参数中的最大值，否则求和。

```
$ python prog.py 1 2 3 4
4
$ python prog.py 1 2 3 4 --sum
101234
```

如果给出无效的参数，会给出一个错误信息:

```
$ python prog.py a b c
usage: prog.py [-h] [--sum] N [N ...]
prog.py: error: argument N: invalid int value: 'a'123
```

## **二、ArgumentParser对象**

使用argparse的第一步是创建一个 ArgumentParser对象，这个ArgumentParser对象中会保存所有将命令行参数转为python数据类型的必需信息。使用 argparse.ArgumentParser创建ArgumentParser对象。

```
argparse.ArgumentParser(prog=None, 
                        usage=None, 
                        epilog=None, 
                        parents=[], 
                        formatter_class=argparse.HelpFormatter, 
                        prefix_chars='-',                            
                        fromfile_prefix_chars=None,              
                        argument_default=None,
                        conflict_handler='error', 
                        add_help=True)12345678910
```

下面介绍上述函数几个常用的参数

### **1.prog**

默认情况下, ArgumentParser对象根据sys.argv[0]的值(不包括路径名)生成帮助信息中的程序名。

```shell
$ cat myprogram.py
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('--foo', help='foo help')
args = parser.parse_args()

$ python myprogram.py --help
usage: myprogram.py [-h] [--foo FOO]  #注意程序名

optional arguments:
 -h, --help  show this help message and exit
 --foo FOO   foo help
$ cd ..
$ python subdir\myprogram.py --help
usage: myprogram.py [-h] [--foo FOO] #注意程序名

optional arguments:
 -h, --help  show this help message and exit
 --foo FOO   foo help12345678910111213141516171819
```

也可以人为指定一个程序名

```
>>> parser = argparse.ArgumentParser(prog='myprogram')
>>> parser.print_help()
usage: myprogram [-h]        #注意程序名

optional arguments:
 -h, --help  show this help message and exit123456
```

在ArgumentParser对象的函数中，通过 %(prog)s可以引用程序名

```
>>> parser = argparse.ArgumentParser(prog='myprogram')
>>> parser.add_argument('--foo', help='foo of the %(prog)s program')
>>> parser.print_help()
usage: myprogram [-h] [--foo FOO]

optional arguments:
 -h, --help  show this help message and exit
 --foo FOO   foo of the myprogram program12345678
```

### **2.usage** 

默认情况下，ArgumentParser对象可以根据参数自动生成用法信息

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('--foo', nargs='?', help='foo help')
>>> parser.add_argument('bar', nargs='+', help='bar help')
>>> parser.print_help()
usage: PROG [-h] [--foo [FOO]] bar [bar ...]  #这是自动生成的usage信息

positional arguments:
 bar          bar help

optional arguments:
 -h, --help   show this help message and exit
 --foo [FOO]  foo help123456789101112
```

同样我们也可以指定usage

```
>>> parser = argparse.ArgumentParser(prog='PROG', usage='%(prog)s [options]')
>>> parser.add_argument('--foo', nargs='?', help='foo help')
>>> parser.add_argument('bar', nargs='+', help='bar help')
>>> parser.print_help()
usage: PROG [options]

positional arguments:
 bar          bar help

optional arguments:
 -h, --help   show this help message and exit
 --foo [FOO]  foo help123456789101112
```

### **3.description**

description 用于展示程序的简要介绍信息，通常包括:这个程序可以做什么、怎么做。在帮助信息中 description位于用法信息与参数说明之间

```
>>> parser = argparse.ArgumentParser(description='A foo that bars')
>>> parser.print_help()
usage: argparse.py [-h]  #用法信息

A foo that bars          # description

optional arguments:      #参数说明 
 -h, --help  show this help message and exit12345678
```

### **4.epilog**

与description类似，程序的额外描述信息，位于参数说明之后

```
>>> parser = argparse.ArgumentParser(
...     description='A foo that bars',
...     epilog="And that's how you'd foo a bar")
>>> parser.print_help()
usage: argparse.py [-h]

A foo that bars

optional arguments:
 -h, --help  show this help message and exit

And that's how you'd foo a bar123456789101112
```

### **5.parents**

有时多个解析器可能有相同的参数集，为了实现代码复用，我们可以将这些相同的参数集提取到一个单独的解析器中，在 
创建其它解析器时通过parents指定父解析器，这样新创建的解析器中就包含了相同的参数集。

```
>>> parent_parser = argparse.ArgumentParser(add_help=False)
>>> parent_parser.add_argument('--parent', type=int)

>>> foo_parser = argparse.ArgumentParser(parents=[parent_parser])
>>> foo_parser.add_argument('foo')
>>> foo_parser.parse_args(['--parent', '2', 'XXX'])
Namespace(foo='XXX', parent=2)

>>> bar_parser = argparse.ArgumentParser(parents=[parent_parser])
>>> bar_parser.add_argument('--bar')
>>> bar_parser.parse_args(['--bar', 'YYY'])
Namespace(bar='YYY', parent=None)123456789101112
```

要注意的一点是：创建父解析器时要指定 add_help=False，否则会报错(ArgumentParser对象会找到两个-h/–help选项)。

### **6.formatter_class**

通过 formatter_class 可以定制帮助信息，通过以下几种格式化类

```
class argparse.RawDescriptionHelpFormatter
class argparse.RawTextHelpFormatter
class argparse.ArgumentDefaultsHelpFormatter
class argparse.MetavarTypeHelpFormatter1234
```

RawDescriptionHelpFormatter 用于定制description和epilog，默认情况下description和epilog是自动换行的。

```
>>> parser = argparse.ArgumentParser(
...     prog='PROG',
...     description='''this description\n
...         was indented weird\n
...             but that is okay''',
...     epilog='''
...             likewise for this epilog whose whitespace will
...         be cleaned up and whose words will be wrapped
...         across a couple lines''')
>>> parser.print_help()
usage: PROG [-h]

this description was indented weird but that is okay

optional arguments:
 -h, --help  show this help message and exit

likewise for this epilog whose whitespace will be cleaned up and whose words
will be wrapped across a couple lines12345678910111213141516171819
```

利用RawDescriptionHelpFormatter实现description和epilog换行

```
>>> parser = argparse.ArgumentParser(
...     prog='PROG',
...     formatter_class=argparse.RawDescriptionHelpFormatter,
...     description=textwrap.dedent('''\
...         Please do not mess up this text!
...         --------------------------------
...             I have indented it
...             exactly the way
...             I want it
...         '''))
>>> parser.print_help()
usage: PROG [-h]

Please do not mess up this text!
--------------------------------
   I have indented it
   exactly the way
   I want it

optional arguments:
 -h, --help  show this help message and exit
RawTextHelpFormatter maintains whitespace for all sorts of help text, including argument descriptions.

ArgumentDefaultsHelpFormatter automatically adds information about default values to each of the argument help messages:123456789101112131415161718192021222324
```

### **7.prefix_chars**

一般情况下，我们使用’-‘作为选项前缀,ArgumentParser也支持自定义选项前缀,通过prefix_chars

```
>>> parser = argparse.ArgumentParser(prog='PROG', prefix_chars='-+')
>>> parser.add_argument('+f')
>>> parser.add_argument('++bar')
>>> parser.parse_args('+f X ++bar Y'.split())
Namespace(bar='Y', f='X')12345
```

### **8.add_help**

是否禁用-h –help选项

```
>>> parser = argparse.ArgumentParser(prog='PROG', add_help=False)
>>> parser.add_argument('--foo', help='foo help')
>>> parser.print_help()
usage: PROG [--foo FOO]

optional arguments:
 --foo FOO  foo help1234567
```

## **三、add_argument()方法**

```
rgumentParser.add_argument(name or flags...[,action][,nargs][,const][,default]
                           [,type][,choices][,required][,help][,metavar][,dest])12
```

### **1.name 或 flags**

指定一个可选参数或位置参数

```
>>> parser.add_argument('-f', '--foo')  #指定一个可选参数
>>> parser.add_argument('bar')          #指定一个位置参数12
```

可选参数是以’-‘为前缀的参数，剩下的就是位置参数

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-f', '--foo')
>>> parser.add_argument('bar')
>>> parser.parse_args(['BAR'])
Namespace(bar='BAR', foo=None)
>>> parser.parse_args(['BAR', '--foo', 'FOO'])
Namespace(bar='BAR', foo='FOO')
>>> parser.parse_args(['--foo', 'FOO'])
usage: PROG [-h] [-f FOO] bar
PROG: error: too few arguments12345678910
```

### **2.action**

action参数指定应该如何处理命令行参数，预置的操作有以下几种: 
action=’store’ 仅仅保存参数值，为action默认值

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo')
>>> parser.parse_args('--foo 1'.split())
Namespace(foo='1')1234
```

action=’store_const’ 与store基本一致，但store_const只保存const关键字指定的值

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='store_const', const=42)
>>> parser.parse_args('--foo'.split())
Namespace(foo=42)
>>> parser.parse_args('--foo 34'.split())
usage: arg_parse.py [-h] [--foo]
arg_parse.py: error: unrecognized arguments: 341234567
```

action=’store_true’或’store_false’ 与store_const一致，只保存True和False

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='store_true')
>>> parser.add_argument('--bar', action='store_false')
>>> parser.parse_args('--foo --bar'.split())
Namespace(bar=False, foo=True)12345
```

action=’append’ 将相同参数的不同值保存在一个list中

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='append')
>>> parser.parse_args('--foo 1 --foo 2'.split())
Namespace(foo=['1', '2'])1234
```

action=’count’ 统计该参数出现的次数

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--verbose', '-v', action='count')
>>> parser.parse_args('-vvv'.split())
Namespace(verbose=3)1234
```

action=’help’ 输出程序的帮助信息

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='help')
>>> parser.parse_args('--foo'.split()) 
usage: arg_parse.py [-h] [--foo]

optional arguments:
  -h, --help  show this help message and exit
  --foo12345678
```

action=’version’ 输出程序版本信息

```
>>> import argparse
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('--version', action='version', version='%(prog)s 2.0')
>>> parser.parse_args(['--version'])
PROG 2.012345
```

除了上述几种预置action，还可以自定义action，需要继承Action并覆盖**call**和**init**方法。

```
>>> class FooAction(argparse.Action):
...     def __init__(self, option_strings, dest, nargs=None, **kwargs):
...         if nargs is not None:
...             raise ValueError("nargs not allowed")
...         super(FooAction, self).__init__(option_strings, dest, **kwargs)
...     def __call__(self, parser, namespace, values, option_string=None):
...         print('%r %r %r' % (namespace, values, option_string))
...         setattr(namespace, self.dest, values)
...
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action=FooAction)
>>> parser.add_argument('bar', action=FooAction)
>>> args = parser.parse_args('1 --foo 2'.split())
Namespace(bar=None, foo=None) '1' None
Namespace(bar='1', foo=None) '2' '--foo'
>>> args
Namespace(bar='1', foo='2')1234567891011121314151617
```

### **3.nargs**

默认情况下 ArgumentParser对象将参数与一个与action一一关联，通过指定 nargs可以将多个参数与一个action相关联。nargs支持值如下： 
N (整数) N个命令行参数被保存在一个list中

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs=2)
>>> parser.add_argument('bar', nargs=1)
>>> parser.parse_args('c --foo a b'.split())
Namespace(bar=['c'], foo=['a', 'b'])12345
```

‘?’ 如果存在该参数且给出了参数值，则从命令行取得该参数，如果存在该参数但未给出参数值，则从const关键字中取得参数值，如果不存在该参数，则将生成默认值。可能表述地不到位，还是看结合代码理解吧

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs='?', const='c', default='d')
>>> parser.add_argument('bar', nargs='?', default='d')
>>> parser.parse_args('XX --foo YY'.split())
Namespace(bar='XX', foo='YY')
>>> parser.parse_args('XX --foo'.split())
Namespace(bar='XX', foo='c')
>>> parser.parse_args(''.split())
Namespace(bar='d', foo='d')123456789
```

‘*’命令行参数被保存在一个list中

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs='*')
>>> parser.add_argument('--bar', nargs='*')
>>> parser.add_argument('baz', nargs='*')
>>> parser.parse_args('a b --foo x y --bar 1 2'.split())
Namespace(bar=['1', '2'], baz=['a', 'b'], foo=['x', 'y'])123456
```

‘+’命令行参数被保存在一个list中，要求至少要有一个参数，否则报错

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('foo', nargs='+')
>>> parser.parse_args('a b'.split())
Namespace(foo=['a', 'b'])
>>> parser.parse_args(''.split())
usage: PROG [-h] foo [foo ...]
PROG: error: too few arguments1234567
```

argparse.REMAINDER 其余的命令行参数保存到一个list中

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('--foo')
>>> parser.add_argument('command')
>>> parser.add_argument('args', nargs=argparse.REMAINDER)
>>> print(parser.parse_args('--foo B cmd --arg1 XX ZZ'.split()))
Namespace(args=['--arg1', 'XX', 'ZZ'], command='cmd', foo='B')123456
```

### **4.default**

如果参数可以缺省，default指定命令行参数不存在时的参数值。

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', default=42)
>>> parser.parse_args('--foo 2'.split())
Namespace(foo='2')
>>> parser.parse_args(''.split())
Namespace(foo=42)123456
```

### **5.type**

默认情况下，ArgumentParser对象将命令行参数保存为字符串。但通常命令行参数应该被解释为另一种类型，如 float或int。通过指定type,可以对命令行参数执行类型检查和类型转换。通用的内置类型和函数可以直接用作type参数的值：

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('foo', type=int)
>>> parser.add_argument('bar', type=open)
>>> parser.parse_args('2 temp.txt'.split())
Namespace(bar=<_io.TextIOWrapper name='temp.txt' encoding='UTF-8'>, foo=2)12345
```

### **6. choices**

将命令行参数的值限定在一个范围内，超出范围则报错 
Example 1

```
>>> parser = argparse.ArgumentParser(prog='game.py')
>>> parser.add_argument('move', choices=['rock', 'paper', 'scissors'])
>>> parser.parse_args(['rock'])
Namespace(move='rock')
>>> parser.parse_args(['fire'])
usage: game.py [-h] {rock,paper,scissors}
game.py: error: argument move: invalid choice: 'fire' (choose from 'rock',
'paper', 'scissors')12345678
```

Example 2

```
>>> parser = argparse.ArgumentParser(prog='doors.py')
>>> parser.add_argument('door', type=int, choices=range(1, 4))
>>> print(parser.parse_args(['3']))
Namespace(door=3)
>>> parser.parse_args(['4'])
usage: doors.py [-h] {1,2,3}
doors.py: error: argument door: invalid choice: 4 (choose from 1, 2, 3)1234567
```

### **7.required**

指定命令行参数是否必需，默认通过-f –foo指定的参数为可选参数。

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', required=True)
>>> parser.parse_args(['--foo', 'BAR'])
Namespace(foo='BAR')
>>> parser.parse_args([])
usage: argparse.py [-h] [--foo FOO]
argparse.py: error: option --foo is required1234567
```

### **8.dest**

dest 允许自定义ArgumentParser的参数属性名称 
Example 1

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('bar')
>>> parser.parse_args('XXX'.split())
Namespace(bar='XXX')1234
```

Example 2

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('-f', '--foo-bar', '--foo')
>>> parser.add_argument('-x', '-y')
>>> parser.parse_args('-f 1 -x 2'.split())
Namespace(foo_bar='1', x='2')
>>> parser.parse_args('--foo 1 -y 2'.split())
Namespace(foo_bar='1', x='2')1234567
```

Example 3

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', dest='bar')
>>> parser.parse_args('--foo XXX'.split())
Namespace(bar='XXX')
```

## 四、其他方法

### parse_known_args()

这个接口是获取命令行里面输入的命令参数，并返回输入参数及未知参数（即未被定义的参数）

FLAGS, unparsed = parser.parse_known_args()

```python
parser = argparse.ArgumentParser()
parser.add_argument(
      "--max_steps",
      type=int,
      default=10,
      help="Number of steps to run trainer.")
FLAGS, unparsed = parser.parse_known_args()
FLAGS.max_steps
->10
```

