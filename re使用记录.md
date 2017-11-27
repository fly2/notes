## re使用记录

re是一个Python的正则表达式包。Python本身str类型提供一些简单的正则操作，该包则提供更强大的正则能力。

**提示：**此包可直接用于处理bytes类型的字符串。

### 匹配模式

匹配模式使用时，直接在函数中的参数用  `flags=re.M`，`flags=re.I`

- re.**I**(re.IGNORECASE): 忽略大小写（括号内是完整写法，下同）
- **M**(MULTILINE): 多行模式，改变'^'和'$'的行为（参见上图）
- **S**(DOTALL): 匹配包括换行符在内的任意字符
- **L**(LOCALE): 使预定字符类 \w \W \b \B \s \S 取决于当前区域设定
- **U**(UNICODE): 使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性
- **X**(VERBOSE): 详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释。以下两个正则表达式是等价的：

### re.match

这个方法将从string的pos下标处起尝试匹配pattern；如果pattern结束时仍可匹配，则返回一个Match对象；如果匹配过程中pattern无法匹配，或者匹配未结束就已到达endpos，则返回None。 
pos和endpos的默认值分别为0和len(string)；re.match()无法指定这两个参数，参数flags用于编译pattern时指定匹配模式。 
**注意：**这个方法并不是完全匹配。当pattern结束时若string还有剩余字符，仍然视为成功。想要完全匹配，可以在表达式末尾加上边界匹配符'$'。 

**注意：**因为match是从设置的pos下标出开始匹配，如果要匹配的不在pos下标开始，将无法匹配。这时应该使用`re.search`

```python
re.match(pattern, string, flags=0)
#pattern为匹配的正则表达式
#string为需要匹配的字符串
#flags为匹配模式
```

### re.search

这个方法用于查找字符串中可以匹配成功的子串。从string的pos下标处起尝试匹配pattern，如果pattern结束时仍可匹配，则返回一个Match对象；若无法匹配，则将pos加1后重新尝试匹配；直到pos=endpos时仍无法匹配则返回None。 
pos和endpos的默认值分别为0和len(string))；re.search()无法指定这两个参数，参数flags用于编译pattern时指定匹配模式。 

```python
re.search(pattern, string, flags=0)
#pattern为匹配的正则表达式
#string为需要匹配的字符串
#flags为匹配模式
```

### re.split

```python
re.split(pattern, string, maxsplit=0, flags=0)
#pattern 为分割匹配的正则
#string 为需要匹配的字符串
#maxsplit=0 按照能够匹配的子串将string分割后返回列表。maxsplit用于指定最大分割次数，不指定将全部分割。 
#flags为匹配模式

#此处\W+表示匹配非单词字符[A-Za-z0-9]1次或无限次
re.split('\W+', 'Words, words, words.')
-->['Words', 'words', 'words', '']
re.split('(\W+)', 'Words, words, words.')
-->['Words', ', ', 'words', ', ', 'words', '.', '']
re.split('\W+', 'Words, words, words.', 1)
-->['Words', 'words, words.']
#匹配模式设置为忽略大小写
re.split('[a-f]+', '0a3B9', flags=re.IGNORECASE)
-->['0', '3', '9']
```

### re.findall

搜索string，以列表形式返回全部能匹配的子串。 

```python
re.findall(pattern, string, flags=0)
#pattern 为分割匹配的正则
#string 为需要匹配的字符串
#flags为匹配模式
```

### re.finditer

搜索string，返回一个顺序访问每一个匹配结果（Match对象）的迭代器。 

```python
re.findall(pattern, string,flags=0)
#pattern 为分割匹配的正则
#string 为需要匹配的字符串
#flags为匹配模式

import re
 

for m in re.finditer('\d+','one1two2three3four4'):
    print(m.group())
-->1
-->2
-->3
-->4
```

### re.sub

使用repl替换string中每一个匹配的子串后返回替换后的字符串。 
当repl是一个字符串时，可以使用\id或\g<id>、\g<name>引用分组，但不能使用编号0。 
当repl是一个方法时，这个方法应当只接受一个参数（Match对象），并返回一个字符串用于替换（返回的字符串中不能再引用分组）。 
count用于指定最多替换次数，不指定时全部替换。 

```python
re.sub(pattern, repl, string, count=0, flags=0)
#pattern 为分割匹配的正则
#repl 替换后的字符串
#string 为需要匹配的字符串
#count=0 count用于指定最多替换次数，不指定时全部替换。 
#flags为匹配模式

string='我是中国人，我在中国'
new_string=re.sub('我','fly',string)
#需要将赋值，不会直接更改原有字符串
print(string)
print(new_string)
-->我是中国人，我在中国
-->fly是中国人，fly在中国
```

### re.subn

执行与sub（）相同的操作，但返回一个元组（new_string，number_of_subs_made）。即新的字符串和替换次数的数组

```python
re.subn(pattern, repl, string, count=0, flags=0)
#pattern 为分割匹配的正则
#repl 替换后的字符串
#string 为需要匹配的字符串
#count=0 count用于指定最多替换次数，不指定时全部替换。 
#flags为匹配模式


string='我是中国人，我在中国'
re.subn('我','fly',string)
-->('fly是中国人，fly在中国', 2)
s=re.subn('我','fly',string)
s[1]
-->2
```



### python正则语法规则



![pyre_ebb9ce1c-e5e8-4219-a8ae-7ee620d5f9f1](.\picture\pyre_ebb9ce1c-e5e8-4219-a8ae-7ee620d5f9f1.png)

### 例子

#### 空格中匹配带符号的百分数

字符串s中的空格可以用s.strip()来去掉s中的空格

```python
td1.text='\r\n                          +0.13%                      '
re.search('(\+|\-).*%',td1.text)
#返回匹配到的结果，如果没有则返回None
```

#### 将文本数字混合的字符串中数字提取出来

```python
import re
re.findall('[1-9]\d*','我19940101年生，读了10年书，卖了3年枣')
->['19940101', '10', '3']
```

#### 匹配最少文本

re中的匹配默认为贪婪模式，即在使用`.*`时会匹配尽可能多的字符。当我们想要匹配最少文本时，只需要写成`.*?`就可以变为懒惰模式，只匹配最少的文本。

```python
a='1234,123,1234,1234,12345,'
#贪婪模式
re.findall('1.*,',a)
->['1234,123,1234,1234,12345,']
#懒惰模式
re.findall('1.*?,',a)
->['1234,', '123,', '1234,', '1234,', '12345,']
```





#### 