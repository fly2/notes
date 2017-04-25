## jieba分词使用

### 分词

```python
import jieba

seg_list = jieba.cut("我来到北京清华大学", cut_all=True)
print("Full Mode: " + "/ ".join(seg_list))  # 全模式
#【全模式】: 我/ 来到/ 北京/ 清华/ 清华大学/ 华大/ 大学

seg_list = jieba.cut("我来到北京清华大学", cut_all=False)
print("Default Mode: " + "/ ".join(seg_list))  # 精确模式
#【精确模式】: 我/ 来到/ 北京/ 清华大学

seg_list = jieba.cut("他来到了网易杭研大厦")  # 默认是精确模式
print(", ".join(seg_list))
#【新词识别】：他, 来到, 了, 网易, 杭研, 大厦    (此处，“杭研”并没有在词典中，但是也被Viterbi算法识别出来了)

seg_list = jieba.cut_for_search("小明硕士毕业于中国科学院计算所，后在日本京都大学深造")  # 搜索引擎模式
print(", ".join(seg_list))
#【搜索引擎模式】： 小明, 硕士, 毕业, 于, 中国, 科学, 学院, 科学院, 中国科学院, 计算, 计算所, 后, 在, 日本, 京都, 大学, 日本京都大学, 深造

#默认会用HMM进行新词发现，新词发现不理想时可以关闭新词发现，使用专用词库进行词频调整。
#用词库设置安全词，词库内的词被切分到一起。安全词.txt格式为每个词一行
safeword=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/safeword.txt',encoding="gb18030").readlines()]
for sf in safeword:
    jieba.suggest_freq(sf, tune=True)
str1='小明硕士毕业于中国科学院计算所，后在日本京都大学深造'
words=list(jieba.cut(str1,HMM=False))

#设置停用词，去掉一些无意义的词。停用词.txt的格式为每词一行。
stopkey=[line.strip() for line in open('/home/weblogic/DATA/private/shangguanxf/cc_txt1/stopword.txt').readlines()] 
str1='小明硕士毕业于中国科学院计算所，后在日本京都大学深造'
words=list(jieba.cut(str1,HMM=False))
line='/'.join(list(set(words1)-set(stopkey)))
```

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
#读取路径所有文件
for root,dirs,files in os.walk('/home/weblogic/DATA/private/shangguanxf/cc_praise/'):
    for filespath in files:
        #
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
```

