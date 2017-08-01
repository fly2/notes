## tensorflow实现word2vec	

word2vec常用的方法有两种CBOW和Skip-Gram。CBOW是根据周围的词去预测中心的词，Skip-Gram是根据中心词去预测上下文的词。

> 如何生成词向量，首先根据具体任务，选一个领域相似的语料，在这个条件下，语料越大越好。然后下载一个 word2vec 的新版（14年9月更新），语料小（小于一亿词，约 500MB 的文本文件）的时候用 Skip-gram 模型，语料大的时候用 CBOW 模型。最后记得设置迭代次数为三五十次，维度至少选 50，就可以了。

1.Skip-Gram

```python
import collections
import math
import os
import random
import zipfile
import numpy as np
import urllib
import tensorflow as tf
```

```python
class Wikipedia:
    def __init__(self,url,cache_dir,vocabulary_size=10000):
        pass
    def __iter__(self):
        #遍历由词语索引构成的列表的页面
        pass
    @property
    def vocabulary_size(self):
        pass
    def encode(self,word):
        #获取一个字符串词语的词汇索引
        pass
    def decode(self,index):
        #根据词汇索引返回字符串词语
        pass
    def _read_pages(self,url):
        #从维基百科转储文件提取单词，并将它们保存到页面文件
        #每个页面都包含一行由空格分隔的一行单词
        pass
    def _build_vocabulary(self,vocabulary_size):
        #统计页面文件中的单词数，并将词汇表中文件中出现频率最高的词语写入文件
        pass
    @classmethod
    def _tokenize(cls,page):
        pass
```

```python
def skipgrams(pages,max_context):
    #依据skip-gram形成训练对
    for words in pages:
        for index,current in enumerate(words):
            #通过随机数，来减少创建距离较远的上下文词语训练样本
            context=random.randint(1,max_context)
            #使用max(0,index-context)为了避免值为负数words取不到值
            for target in words[max(0,index-context):index]:
                yield current,target
            #此处最大值越界依然只会取到list结尾的数，不会造成报错
            for target in words[index+1:index+context+1]:
                yield current,target
```

```python
def batched(iterator,batch_size):
    #将数据流数据组织为批数据，并用numpy数组生成它们
    while True:
        data=np.zeros(batch_size)
        target=np.zeros(batch_size)
        for index in range(batch_size):
            data[index],target[index]=next(iterator)
        yield data,target
```

```python
class EmbeddingModel:
    def __init__(self,data,target,params):
        self.data=data
        self.target=target
        self.params=params
        self.embeddings
        self.cost
        self.optimize
    @lazy_property
    def embeddings(self):
        pass
    @lazy_property
    def optimize(self):
        pass
    @lazy_property
    def cost(self):
        pass
```

