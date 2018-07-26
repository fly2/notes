## 词向量模型

**注意：**用词向量生成文本向量时使用词向量的均值而不使用向量和可以避免文本长度对文本向量造成的影响。

### 参数说明及使用

```python
model = Word2Vec(sentences=None, size=100, alpha=0.025, window=5, min_count=5, max_vocab_size=None, sample=0.001, seed=1, workers=3, min_alpha=0.0001, sg=0, hs=0, negative=5, cbow_mean=1, hashfxn=<built-in function hash>, iter=5, null_word=0, trim_rule=None, sorted_vocab=1, batch_words=10000)
#sentences：可以是一个list,每一段文本list中的一个集合，每个集合又按照词顺序组成list，对于大语料集，建议使用BrownCorpus,Text8Corpus或lineSentence构建。
#size表示词嵌入的特征维度.大的size需要更多的训练数据,但是效果会更好. 推荐值为几十到几百。
#window表示词的窗口，指当前词与预测词的最大距离
#min_count表示忽视词出现次数低于此值的词
#默认sg=0，为CBOW，否则sg=1，为skip-gram
#seed为函数的随机化设置种子，确保每次随机结果一样。
#alpha: 是学习速率
#min_count: 可以对字典做截断. 词频少于min_count次数的单词会被丢弃掉, 默认值为5
#max_vocab_size: 设置词向量构建期间的RAM限制。如果所有独立单词个数超过这个，则就消除掉其中最不频繁的一个。每一千万个单词需要大约1GB的RAM。设置成None则没有限制。
#sample: 高频词汇的随机降采样的配置阈值，默认为1e-3，范围是(0,1e-5)
#workers参数控制训练的并行数。
#hs: 如果为1则会采用hierarchica·softmax技巧。如果设置为0（defau·t），则negative sampling会被使用。
#negative: 如果>0,则会采用负采样(negativesamp·ing)，用于设置多少个负采样词数(noise words)
#cbow_mean: 如果为0，则采用上下文词向量的和，如果为1（defau·t）则采用均值。只有使用CBOW的时候才起作用。
#hashfxn： hash函数来初始化权重。默认使用python的hash函数
#iter： 迭代次数，默认为5
#trim_rule： 用于设置词汇表的整理规则，指定那些单词要留下，哪些要被删除。可以设置为None（min_count会被使用）或者一个接受()并返回RU·E_DISCARD,uti·s.RU·E_KEEP或者uti·s.RU·E_DEFAU·T的函数。
#sorted_vocab： 如果为1（defau·t），则在分配word index 的时候会先对单词基于频率降序排序。
#batch_words：每一批的传递给线程的单词的数量，默认为10000

#导入词向量模块
from gensim.models import Word2Vec

#语料生成
#BrownCorpus是迭代来自Brown语料库（NLTK数据的一部分）的句子。
#Text8Corpus从“text8”语料库迭代句子，从http://mattmahoney.net/dc/text8.zip中解压缩。
from gensim.models.word2vec import LineSentence
#LineSentence返回的是两重列表，每行为一个列表，行列表由词列表组成
sentences = LineSentence('myfile.txt')#txt格式为分好词的句子，词以空格隔开。一行一句话。
sentences = LineSentence('compressed_text.txt.bz2')
sentences = LineSentence('compressed_text.txt.gz')

#训练模型
import multiprocessing
model = Word2Vec(doc_list,size=400, window=5, min_count=5,workers=multiprocessing.cpu_count())
#更新模型参数
model.train(sentences, total_words=None, word_count=0, total_examples=None, queue_factor=2, report_delay=1.0)
#从句子序列更新模型的神经权重（可以是一次性的生成器流）。 对于Word2Vec，每个句子必须是unicode字符串的列表。 （子类可以接受其他示例。）为了支持从（初始）α到min_alpha的线性学习率衰减，应提供total_examples（句子数/行数）或total_words（句子中的原始单词数），除非句子与用于初始构建的句子相同 词汇。

#删除中间参数
model.delete_temporary_training_data(replace_word_vectors_with_normalized=False)
#丢弃在训练和得分中使用的参数。 如果您确定已完成模型训练。 如果replace_word_vectors_with_normalized被设置，忘记原始向量，只保留归一化的向量=节约大量的内存！

#得到索引到vocab字典的映射，3.3后index2word函数移动到model.wv下面
model = Word2Vec(size=h, window=5, min_count=0,workers=24,iter=1)
model.build_vocab(seg)  
model.train(seg)
model.index2word
->['say', 'cat', 'meow', 'woof', 'dog']
#3.3后使用以下函数得到vocab表
model.wv.index2word


#得到词向量内容
model['再见']

#找出前N个最相似的词。积极的词有助于积极的相似性，消极的词则相反。加法运算
most_similar(positive=[], negative=[], topn=10, restrict_vocab=None, indexer=None)
#该方法计算给定单词投影权重向量的简单平均值(正例权重为1，负例为-1。即正例-负例)与模型中每个单词的向量之间的余弦相似性。该方法对应于原始word2vec实现中的word-analogy和distance 脚本。如果topn为False，most_similar返回相似度分数的向量。
#restrict_vocab 是一个可选的整数，它限制了搜索最相似值的向量的范围。例如，restrict_vocab = 10000将只检查词汇顺序中前10000个单词向量。（如果您按频率降序排序词汇表，这将很有意义。）
例：
>>> trained_model.most_similar(positive=['woman','king'], negative=['man'])
[('queen', 0.50882536), ...]

#使用Omer Levy和Yoav Goldberg在[4]中提出的乘法组合目标寻找前N个最相似的词。积极的词对于相似性仍然是积极地，而消极词是负面地，但对一个大距离支配计算有较小的易感性。
most_similar_cosmul(positive=[], negative=[], topn=10)
#附加的正或负例子分别对分子或分母做出贡献。具体见https://github.com/RaRe-Technologies/gensim/blob/686e97579a61edf20908afd4aba90bfaf0fce340/gensim/models/keyedvectors.py
例：
>>> trained_model.most_similar_cosmul(positive=['baghdad','england'], negative=['london'])
[(u'iraq', 0.8488819003105164), ...]


#根据词得到最相似的词
similar_by_word(word, topn=10, restrict_vocab=None)
#如果topn为False，similar_by_word返回相似性分数的向量。restrict_vocab是一个可选整数，它限制了搜索最相似值的向量的范围。例如，restrict_vocab = 10000将只检查词汇顺序中前10000个单词向量。（如果您按频率降序排序词汇表，这将很有意义。）
例：
>>> trained_model.similar_by_word('graph')
[('user', 0.9999163150787354), ...]

#通过向量找出前N个最相似的词。
similar_by_vector(vector, topn=10, restrict_vocab=None)
#如果topn为False，similar_by_vector返回相似性分数的向量。restrict_vocab是一个可选整数，它限制了搜索最相似值的向量的范围。例如，restrict_vocab = 10000将只检查词汇顺序中前10000个单词向量。（如果您按频率降序排序词汇表，这将很有意义。）
例：
>>> trained_model.similar_by_vector([1,2])
[('survey', 0.9942699074745178), ...]

similarity(w1, w2)
#计算两个词之间的余弦相似度。
例：
>>> trained_model.similarity('woman','man')
0.73723527
>>> trained_model.similarity('woman','woman')
1.0

#找出最不匹配的词
model.doesnt_match("breakfast cereal dinner lunch".split())
-->'cereal'

#保存模型,后缀对保存加载没有影响，可以任意命名后缀
model.save('/home/weblogic/DATA/private/shangguanxf/cc_txt2/word2vec.bin')
#加载模型
model=gensim.models.Word2Vec.load("/home/weblogic/DATA/private/shangguanxf/cc_txt2/word2vec.bin")
```
### 文档

[使用示例](https://github.com/RaRe-Technologies/gensim/blob/develop/docs/notebooks/word2vec.ipynb)

[在线更新模型示例](https://github.com/RaRe-Technologies/gensim/blob/develop/docs/notebooks/online_w2v_tutorial.ipynb)

## 文档向量模型

### 参数说明及使用

```python
import gensim
from gensim.models.doc2vec import TaggedLineDocument,LabeledSentence 
Doc2Vec(documents=None, dm_mean=None, dm=1, dbow_words=0, dm_concat=0, dm_tag_count=1, docvecs=None, docvecs_mapfile=None, comment=None, trim_rule=None, **kwargs)
#documents为list，list内一列为文档词的list，另一列为tag。可以通过TaggedLineDocument直接从txt中生成sentences，txt的格式为一个文档一行，每个词之间以空格隔开
#dm_mean 默认为0，使用文档内词向量的和，dm_mean=1时使用向量的平均值。（仅在dm被用在非拼接模型时使用）
#dm定义训练算法。默认dm=1,使用PV-DM。dm=0,使用PV-DBOW
#dbow_words 如果设为1，训练word-vectors (用skip-gram方式) 的同时训练 DBOW doc-vector。默认是0 (仅训练doc-vectors时更快)。
#dm_concat 如果为1，使用上下文词向量的拼接，默认是0。注意，拼接的结果是一个更大的模型，输入的大小不再是一个词向量（采样或算术结合），而是标签和上下文中所有词结合在一起的大小
#dm_tag_count 每个文件期望的文本标签数，在使用dm_concat模式时默认为1。

#语料生成
documents=TaggedLineDocument('/home/weblogic/DATA/private/shangguanxf/cc_txt2/语音文本分词.txt')
#语音文本分词为一个文档一行，内部分词以空格分开

#训练模型
model = Doc2Vec(documents, size=400, window=5, min_count=5,iter=5,workers=multiprocessing.cpu_count())

#先生成新文本的文本向量
inferred_vector = model.infer_vector(['解除','核实','张女士','军民','状况','权益','签收','工号','服务满意','损失','投保','保险期限','无条件'])
#得到最相似的文档index和相似度
sims = model.docvecs.most_similar([inferred_vector], topn=3)  
print(sims) 
#保存模型
model.save('/home/weblogic/DATA/private/shangguanxf/cc_txt2/doc2vec.bin')
#加载模型
model= gensim.models.Doc2Vec.load("/home/weblogic/DATA/private/shangguanxf/cc_txt2/doc2vec.bin")
```

## lda（隐狄利克雷分布）

得到文档主题分布。

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
query= doc_dict.doc2bow('北京市/保额/交费/明白/无条件/核对/损失/几号/工作/健康/如实'.split('/'))
ldamodel[query]
#得到该查询分类到每个主题的概率
```

##  wikicorpus

用于处理wiki数据的模块，这个模块进行操作时文件被即时提取，整个文件可以保持压缩在磁盘上。

下面的token在英文中指代词，中文指代句子。

```python
gensim.corpora.wikicorpus.WikiCorpus(fname, processes=None, lemmatize=True, dictionary=None, filter_namespaces=('0', ), tokenizer_func=<function tokenize>, article_min_tokens=50, token_min_len=2, token_max_len=15, lower=True)
#fname 文件路径，支持两种命名格式的文件
'''
<LANG>wiki-<YYYYMMDD>-pages-articles.xml.bz2
<LANG>wiki-latest-pages-articles.xml.bz2
'''
#processes 要运行的进程数量，默认为cpu数量 - 1
#lemmatize 是否使用词形化而不是简单的正则表达式正则化。 如果安装了pattern包，则默认为True。
#dictionary 字典，如果没有提供，这将扫描一次语料库，以确定其词汇
#filter_namespaces 命名空间
#tokenizer_func 用来正则化的函数，默认使用tokenize()。需要支持以下接口tokenizer_func(text: str, token_min_len: int, token_max_len: int, lower: bool)->list of str 
#article_min_tokens=50 文章中的最小token数。 如果token较少，则条目将被忽略。
#token_min_len 最小token长度
#token_max_len=15 最大token长度
#lower 如果为True，转换所有字母为小写

#获取所有文本 
#在gensim 3.4.0之前,获取的文本是bytes格式的，需要注意转换
with open('zhwiki.txt') as f:
    for i in wiki.get_texts():
    	f.write(b' '.join(i).decode('utf-8') + '\n')

#从gensim 3.4.0时获取的文本为unicode格式，无需转换
n=0
with open(wiki_output,'w') as output:
    for i in wiki.get_texts():
        output.write(' '.join(i)+ "\n")
        n=n+1
        if n%10000==0:
            print("Saved " + str(n) + " articles")
```

