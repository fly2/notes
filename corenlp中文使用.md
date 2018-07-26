## corenlp使用

[python使用方式](https://stanfordnlp.github.io/CoreNLP/other-languages.html)

### nltk中文使用

nltk本身没有中文模型，可以通过调用stanford的corenlp来实现中文的相关功能。

#### 安装

1. [corenlp](https://stanfordnlp.github.io/CoreNLP/index.html#download)：corenlp的下载页面
2. [Jar1.8](http://pan.baidu.com/s/1miubwq0) ：如果你本机是Java 8以上版本，可以不用下载了
3. [NLTK](https://pan.baidu.com/s/1pKA9XuN) ：这个工具包提供Standford NLP接口

#### 使用

##### 分词

```python
>>> from nltk.tokenize.stanford_segmenter import StanfordSegmenter
>>> segmenter = StanfordSegmenter(
    path_to_jar=r"E:\tools\stanfordNLTK\jar\stanford-segmenter.jar",
    path_to_slf4j=r"E:\tools\stanfordNLTK\jar\slf4j-api.jar",
    path_to_sihan_corpora_dict=r"E:\tools\stanfordNLTK\jar\data",
    path_to_model=r"E:\tools\stanfordNLTK\jar\data\pku.gz",
    path_to_dict=r"E:\tools\stanfordNLTK\jar\data\dict-chris6.ser.gz"
)
>>> str="我在博客园开了一个博客，我的博客名叫伏草惟存，写了一些自然语言处理的文章。"
>>> result = segmenter.segment(str)
>>> result
```

##### 命名实体识别

```python
 from nltk.tag import StanfordNERTagger
>>> chi_tagger = StanfordNERTagger(model_filename=r'E:\tools\stanfordNLTK\jar\classifiers\chinese.misc.distsim.crf.ser.gz',path_to_jar=r'E:\tools\stanfordNLTK\jar\stanford-ner.jar')
>>> for word, tag in  chi_tagger.tag(result.split()):
    print(word,tag)
```

##### 词性标注

```python
>>> from nltk.tag import StanfordPOSTagger
>>> chi_tagger = StanfordPOSTagger(model_filename=r'E:\tools\stanfordNLTK\jar\models\chinese-distsim.tagger',path_to_jar=r'E:\tools\stanfordNLTK\jar\stanford-postagger.jar')
>>> result
'四川省 成都 信息 工程 大学 我 在 博客 园 开 了 一个 博客 ， 我 的 博客 名叫 伏 草 惟 存 ， 写 了 一些 自然语言 处理 的 文章 。\r\n'
>>> print(chi_tagger.tag(result.split()))
```

##### 句法分析

```python
>>> from nltk.parse.stanford import StanfordParser
>>> chi_parser = StanfordParser(r"E:\tools\stanfordNLTK\jar\stanford-parser.jar",r"E:\tools\stanfordNLTK\jar\stanford-parser-3.6.0-models.jar",r"E:\tools\stanfordNLTK\jar\classifiers\chinesePCFG.ser.gz")
>>> sent = u'北海 已 成为 中国 对外开放 中 升起 的 一 颗 明星'
>>> print(list(chi_parser.parse(sent.split())))
```

##### 依存句法分析

```python
>>> from nltk.parse.stanford import StanfordDependencyParser
>>> chi_parser = StanfordDependencyParser(r"E:\tools\stanfordNLTK\jar\stanford-parser.jar",r"E:\tools\stanfordNLTK\jar\stanford-parser-3.6.0-models.jar",r"E:\tools\stanfordNLTK\jar\classifiers\chinesePCFG.ser.gz")
>>> res = list(chi_parser.parse(u'四川 已 成为 中国 西部 对外开放 中 升起 的 一 颗 明星'.split()))
>>> for row in res[0].triples():
    print(row)
```

### 其他使用corenlp的方法

[python-stanford-corenlp](https://github.com/stanfordnlp/python-stanford-corenlp)官方python调用

[stanfordcorenlp](https://github.com/Lynten/stanford-corenlp)个人的python封装

#### 其他nlp包（python）

[pyhanlp](https://github.com/hankcs/pyhanlp)