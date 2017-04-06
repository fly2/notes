1.词向量模型，对词的表示进行降维
```python
model = Word2Vec(sentences, size=100, window=5, min_count=5, workers=4,sg=0，seed=0)
#size表示词嵌入的特征维度.大的size需要更多的训练数据,但是效果会更好. 推荐值为几十到几百。
#window表示词的窗口，指当前词与预测词的最大距离
#min_count表示忽视词出现次数低于此值的词
#默认sg=0，为CBOW，否则sg=1，为skip-gram
#seed为函数的随机化设置种子，确保每次随机结果一样。
#sentences：可以是一个list，对于大语料集，建议使用BrownCorpus,Text8Corpus或lineSentence构建。
#alpha: 是学习速率
#min_count: 可以对字典做截断. 词频少于min_count次数的单词会被丢弃掉, 默认值为5
#max_vocab_size: 设置词向量构建期间的RAM限制。如果所有独立单词个数超过这个，则就消除掉其中最不频繁的一个。每一千万个单词需要大约1GB的RAM。设置成None则没有限制。
#sample: 高频词汇的随机降采样的配置阈值，默认为1e-3，范围是(0,1e-5)
#workers参数控制训练的并行数。
#hs: 如果为1则会采用hierarchica·softmax技巧。如果设置为0（defau·t），则negative sampling会被使用。
#negative: 如果>0,则会采用negativesamp·ing，用于设置多少个noise words
#cbow_mean: 如果为0，则采用上下文词向量的和，如果为1（defau·t）则采用均值。只有使用CBOW的时候才起作用。
#hashfxn： hash函数来初始化权重。默认使用python的hash函数
#iter： 迭代次数，默认为5
#trim_rule： 用于设置词汇表的整理规则，指定那些单词要留下，哪些要被删除。可以设置为None（min_count会被使用）或者一个接受()并返回RU·E_DISCARD,uti·s.RU·E_KEEP或者uti·s.RU·E_DEFAU·T的函数。
#sorted_vocab： 如果为1（defau·t），则在分配word index 的时候会先对单词基于频率降序排序。
#batch_words：每一批的传递给线程的单词的数量，默认为10000
```
2. lda（隐狄利克雷分布），得到文档的主题分布

```python
import gensim
from gensim import corpora
import pandas as pd
df1=pd.read_csv('/home/weblogic/DATA/private/shangguanxf/cc_txt2/疑似误导语音文本分词.csv')
doc_list=[]
for i in df1.分词结果:
    doc_list.append(i.split('/'))
#doc_list需要为文档的列表，每个文档又由词的列表组成
#得到一个词语字典
doc_dict=corpora.Dictionary(doc_list)
#将用字符串表示的文档转换为用id表示的文档向量
corpus=[doc_dict.doc2bow(text) for text in doc_list]
lda=gensim.models.ldamodel.LdaModel
ldamodel=lda(corpus,num_topics=5,id2word=doc_dict,passes=30,random_state=1)
#corpus语料集
#num_topics划分的主题数
#id2word表示词id到每个文档的映射，用于决定词汇表的规模以及调试和主题打印
```

   ​