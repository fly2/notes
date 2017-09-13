## jieba分词使用

### 分词

- `jieba.cut` 方法接受三个输入参数: 需要分词的字符串；cut_all 参数用来控制是否采用全模式；HMM 参数用来控制是否使用 HMM 模型。

- `jieba.cut_for_search` 方法接受两个参数：需要分词的字符串；是否使用 HMM 模型。该方法适合用于搜索引擎构建倒排索引的分词，粒度比较细

```python
import jieba

seg_list = jieba.cut("我来到北京清华大学", cut_all=True)
print("Full Mode: " + "/ ".join(seg_list))  # 全模式
【全模式】: 我/ 来到/ 北京/ 清华/ 清华大学/ 华大/ 大学

seg_list = jieba.cut("我来到北京清华大学", cut_all=False)
print("Default Mode: " + "/ ".join(seg_list))  # 精确模式
【精确模式】: 我/ 来到/ 北京/ 清华大学

seg_list = jieba.cut("他来到了网易杭研大厦")  # 默认是精确模式
print(", ".join(seg_list))
【新词识别】：他, 来到, 了, 网易, 杭研, 大厦    (此处，“杭研”并没有在词典中，但是也被Viterbi算法识别出来了)

seg_list = jieba.cut_for_search("小明硕士毕业于中国科学院计算所，后在日本京都大学深造")  # 搜索引擎模式
print(", ".join(seg_list))
【搜索引擎模式】： 小明, 硕士, 毕业, 于, 中国, 科学, 学院, 科学院, 中国科学院, 计算, 计算所, 后, 在, 日本, 京都, 大学, 日本京都大学, 深造
```
- `jieba.lcut` 以及 `jieba.lcut_for_search` 直接返回 list。

- `jieba.Tokenizer(dictionary=DEFAULT_DICT)` 新建自定义分词器，可用于同时使用不同词典，DEFAULT_DICT为设置字典的路径。。`jieba.dt` 为默认分词器，所有全局分词相关函数都是该分词器的映射。

  ```python
  #设置新的分词器，当词典格式不一致或缺省会报错。只做分词的话可以缺省词性
  import jieba
  new_dt=jieba.Tokenizer(dictionary='d:/new_dict.txt')
  ```

  ​

### 词典格式 

  `词 频数 词性`，即每一行分三部分：词语、词频（可省略）、词性（可省略），用空格隔开编码格式需为utf-8，当词典格式不一致或缺省会报错。只做分词的话可以缺省词性。

```python
  AT&T 3 nz
  B超 3 n
  c# 3 nz
  C# 3 nz
  c++ 3 nz
  C++ 3 nz
  T恤 4 n
  A座 3 n
  A股 3 n
  A型 3 n
  A轮 3 n
  AA制 3 n
  AB型 3 n
  B座 3 n
  B股 3 n
  B型 3 n
  B超 3 n
  B轮 3 n
```


### 调整词频

jieba调整词频常用的有`add_word(word, freq=None, tag=None)`和`del_word(word)`，`suggest_freq(segment, tune=True)`但其实核心是jieba.add_word(word, freq)这个函数，另外两个函数内部都在调用`add_word()`。

**注意：自动计算的词频在使用 HMM 新词发现功能时可能无效。**

#### 加载词典

在原有词库的基础上加载新词典。词典格式为标准字典格式，可省略词频和词性。但要为utf-8编码。更改分词器（默认为 `jieba.dt`）的 `tmp_dir` 和 `cache_file` 属性，可分别指定缓存文件所在的文件夹及其文件名，用于受限的文件系统。

- 用法： jieba.load_userdict(file_name) # file_name 为文件类对象或自定义词典的路径

#### get_FREQ(word,freq)

得到word的词频，当该词在表中不存在时，会显示freq值，但不会改变语料库词频

```python
#示例
print(jieba.get_FREQ('中国好邻居'))
print(jieba.get_FREQ('中国好邻居',21))
print(jieba.get_FREQ('中国好邻居',))
#结果
>>
None
21
None
```

#### add_word(word, freq=None, tag=None)

更改语料库中词的词频和词性，如果不存在则新添该词进入语料库

```python
add_word(word, freq=None, tag=None)
#word 词
#freq 频数
#tag 词性

#示例
#更改词频
print(jieba.get_FREQ('中国'))
jieba.add_word('中国',200)
print(jieba.get_FREQ('中国',21))
#结果
>>
129470
200
#新加词汇
print(jieba.get_FREQ('中国好邻居'))
jieba.add_word('中国好邻居',21)
print(jieba.get_FREQ('中国好邻居'))
#结果
>>
None
21
```

#### suggest_freq(segment, tune=True)

调节单个词语的词频，使其能（或不能）被分出来。能被分出来是指都是单字时会把它合并成一个词，当所用字已和其他字结合成词后，将难以分出所需词。

```python
import jieba
i='我是隔壁老王，我要买保险'
jieba.lcut(i)
>>
['我', '是', '隔壁', '老王', '，', '我要', '买', '保险', '。']

#调整词频，使'隔壁老王'能被分出来
jieba.suggest_freq('隔壁老王', tune=True)
jieba.lcut(i)
>>
['我', '是', '隔壁老王', '，', '我要', '买', '保险', '。']

#调整词频，使'壁老'分出，但由于词已被结合，所以难以分出。
jieba.suggest_freq('壁老',tune=True)
print(jieba.lcut(i))
>>
['我', '是', '隔壁老王', '，', '我要', '买', '保险', '。']

#将'隔壁老王'拆分，使其他词可以被分出
jieba.suggest_freq(('隔','壁','老','王'),True)
print(jieba.lcut(i))
#分出'隔壁'，'老王'
>>
['我', '是', '隔壁', '老王', '，', '我要', '买', '保险', '。']

jieba.suggest_freq(('隔','壁'),True)
jieba.suggest_freq(('老','王'),True)
print(jieba.lcut(i))
#分出'壁老'
>>
['我', '是', '隔', '壁老', '王', '，', '我要', '买', '保险', '。']

#默认会用HMM进行新词发现，新词发现不理想时可以关闭新词发现，使用专用词库进行词频调整。
#用词库设置安全词，词库内的词被切分到一起。安全词.txt格式为每个词一行
safeword=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/safeword.txt',encoding="gb18030").readlines()]
for sf in safeword:
    jieba.suggest_freq(sf, tune=True)
str1='小明硕士毕业于中国科学院计算所，后在日本京都大学深造'
words=list(jieba.cut(str1,HMM=False))


```

#### del_word(word)

通过调用`add_word`将词的词频调整为0，来避免无效词产生。

```python
import jieba
#需要将HMM=False，否则发现新词会影响del_word的效果，导致词重新发现
i='司机，秋名山怎么走'
print(jieba.lcut(i))
jieba.del_word('司机')
print(jieba.get_FREQ('司机'))
print(jieba.lcut(i,HMM=False))
>>
['司机', '，', '秋', '名山', '怎么', '走']
0
['司', '机', '，', '秋', '名山', '怎么', '走']
#HMM=True,将发现新词
print(jieba.lcut(i))
>>
['司机', '，', '秋', '名山', '怎么', '走']
```

**使用实例：**

```python
#stopword为停用词表，每行为一个停用词
stopkey=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/stopword.txt').readlines()] 
for sk in stopkey:
    jieba.del_word(sk)
```

**注意：**

除了使用`del_word`方法外，还可以使用列表集合取差集进行去除无效词，但此法会打乱分词顺序，如果后续需要进行语义分析，会产生较大影响。可以用在tfidf生成**关键词抽取**或以**tfidf向量分类**中。

```python
#设置停用词，去掉一些无意义的词。停用词.txt的格式为每词一行。
stopkey=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/stopword.txt').readlines()] 
str1='小明硕士毕业于中国科学院计算所，后在日本京都大学深造'
words=list(jieba.cut(str1,HMM=False))
#通过对列表取差集进行去除无效词，
line='/'.join(list(set(words1)-set(stopkey)))
```

### 词性标注

jieba支持分词时进行词性标注。

```python
import jieba.posseg as pseg
words = pseg.cut("我爱北京天安门")
#words是一个generator object，需要用for循环导出
for word,flag in words:
    print(word,flag)
>>
我 r
爱 v
北京 ns
天安门 ns

#使用lcut可以直接得到list，但list为pair格式
words=pseg.lcut("我爱北京天安门")
words
>>
[pair('我', 'r'), pair('爱', 'v'), pair('北京', 'ns'), pair('天安门', 'ns')]

words[0]
>>
pair('我', 'r')
```

[词性标注解释](http://fhqllt.iteye.com/blog/947917)

| 符号   | 标注   | 符号   | 标注   |
| ---- | ---- | ---- | ---- |
| a    | 形容词  | b    | 区别词  |
| c    | 连词   | d    | 副词   |
| e    | 叹词   | g    | 语素词  |
| h    | 前接成分 | i    | 习用语  |
| j    | 简称   | k    | 后接成分 |
| m    | 数次   | n    | 普通名词 |
| nd   | 方位名词 | nh   | 人名   |
| ni   | 机构名  | nl   | 处所名词 |
| ns   | 地名   | nt   | 时间词  |
| nz   | 其他专名 | o    | 拟声词  |
| p    | 介词   | q    | 量词   |
| r    | 代词   | u    | 助词   |
| v    | 动词   | wp   | 标点符号 |
| ws   | 字符串  | x    | 非语素词 |
|      |      |      |      |

### 分词示例

```python
import jieba
import re
import pandas as pd
import os
#设置停用词
stopkey=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/stopword.txt').readlines()] 
#stopkey=[line.strip().decode('utf-8') for line in open('/home/weblogic/DATA/private/shangguanxf/txt_anaysis/stopword.txt').readlines()] 
#stop_list = open('/home/weblogic/DATA/private/shangguanxf/txt_anaysis/stopword.txt',encoding="utf-8")
#设置安全词
safeword=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/safeword.txt',encoding="gb18030").readlines()]
for sf in safeword:
    jieba.suggest_freq(sf, tune=True)
#还可以通过jieba.load_userdict(file_name)设置文件类对象或自定义词典路径，自定义词典需为utf-8编码。    
    
#读取路径所有文件
for root,dirs,files in os.walk('/home/weblogic/DATA/private/shangguanxf/cc_praise/'):
    for filespath in files:
        #只对文件名包含'合并.csv'的文件进行操作
        if filespath.find('合并.csv')>-1:
            df=pd.read_csv(os.path.join(root,filespath),encoding="gb18030")
            #填充na
            df=df.fillna('无')
            f=df.客户录音文本
            wordlist=[]
            wordlen=[]
            for i in f:
                words=list(jieba.cut(i,HMM=False))
                words1=[word for word in words if len(word)>1]
                #去除停用词，但用这种方法去除停用词会打乱词的顺序
                seg="/".join(list(set(words1)-set(stopkey)))
                seg=re.sub('CU|OP','',seg,count=0,flags=0)
                seg=re.sub('[0-9]','',seg,count=0,flags=0)
                seg=re.sub('//','/',seg,count=0,flags=0)
                seg=re.sub('//','/',seg,count=0,flags=0)
                wordlist.append(seg)
                wordlen.append(len(seg))
            df['分词结果']=wordlist
            df=df.reset_index(drop=True)
            filespath1=filespath.replace('.csv','分词.csv')
            df.to_csv(os.path.join('/home/weblogic/DATA/private/shangguanxf/cc_praise/',filespath1),encoding='gb18030',index=False)
print('end')
```

### tfidf关键词抽取

返回文本中tf-idf权重最大的词。可以直接使用`jieba.analyse.extract_tags`，也可以自定义idf词频库（`jieba.analyse.set_idf_pathk`）后再用`jieba.analyse.extract_tags`得到tf-idf的权重top词

在做回访录音的tf-idf的top词时，需要对一些无意义的低频词进行处理，否则将导致名字，地名等无意义的低频词idf值很高，影响tf-idf值得有效性。

```python
#得到文本中tf-idf最大的top词
jieba.analyse.extract_tags(sentence, topK=20, withWeight=False, allowPOS=())
#sentence 为待提取的文本，无需分词
#topK 为返回几个 TF/IDF 权重最大的关键词，默认值为 20
#withWeight 为是否一并返回关键词权重值，默认值为 False
#allowPOS 仅包括指定词性的词，默认值为空，即不筛选

#设置自定义idf词频语料库
jieba.analyse.set_idf_path(file_name) # file_name为自定义语料库的路径
#设置停用词
jieba.analyse.set_stop_words(file_name) #file_name为停用词的路径
```

### textrank关键词抽取

可以直接使用`jieba.analyse.textrank`函数对句子用textrank算法得到词权重，也可以先定义TextRank，在TextRank中设置窗口大小，再计算textrank权重。

将待抽取关键词的文本进行分词，以固定窗口大小(默认为5，通过span属性调整)，词之间的共现关系，构建图。计算图中节点的PageRank，注意是**无向带权图**。

```python
#得到文本中textrank最大的top词
jieba.analyse.textrank(sentence, topK=20, withWeight=False, allowPOS=('ns', 'n', 'vn', 'v')) 直接使用，接口相同，注意默认过滤词性。、
#sentence 为待提取的文本，无需分词
#topK 为返回几个 TF/IDF 权重最大的关键词，默认值为 20
#withWeight 为是否一并返回关键词权重值，默认值为 False
#allowPOS 注意默认过滤词性。

#设置窗口大小需要自行定义textrank
tr=jieba.analyse.TextRank()
#设置窗口大小，默认为5
tr.span=4
tr.textrank(i, topK=10,withWeight=True)
#设置停用词
jieba.analyse.set_stop_words(file_name) #file_name为停用词的路径
```

