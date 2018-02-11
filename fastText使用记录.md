## fastText使用记录

### 注意

下面的函数示例都是在文件路径下执行，如需更换位置请注意改改函数调用路径。

### 安装

目前只支持Mac OS和Linux，具体安装要求见[官网](https://github.com/facebookresearch/fastText#requirements)

对于 `word-similarity`功能需要安装： 

- Python 2.6 or newer
- NumPy & SciPy

对于使用中绑定的python需要安装：

- Python version 2.7 or >=3.4
- NumPy & SciPy
- [pybind11](https://github.com/pybind/pybind11)

#### 使用make构建(推荐)

```shell
$ wget https://github.com/facebookresearch/fastText/archive/v0.1.0.zip
$ unzip v0.1.0.zip
$ cd fastText-0.1.0
$ make
```

#### 使用python构建

```shell
$ git clone https://github.com/facebookresearch/fastText.git
$ cd fastText
$ pip install .
```

#### 所有函数

[用法说明](https://fasttext.cc/docs/en/supervised-tutorial.html)

```shell
usage: fasttext <command> <args>

fasttext支持的命令:

  supervised              train a supervised classifier
  quantize                quantize a model to reduce the memory usage
  test                    evaluate a supervised classifier
  predict                 predict most likely labels
  predict-prob            predict most likely labels with probabilities
  skipgram                train a skipgram model
  cbow                    train a cbow model
  print-word-vectors      print word vectors given a trained model
  print-sentence-vectors  print sentence vectors given a trained model
  nn                      query for nearest neighbors
  analogies               query for analogies
```

### 分类	

```shell
#参数说明
#输入命令，input和output参数为空可得
$ ./fasttext supervised
The following arguments are mandatory:
  -input              training file path
  -output             output file path

The following arguments are optional:
  -verbose            verbosity level [2]

The following arguments for the dictionary are optional:
  -minCount           minimal number of word occurences [1]
  -minCountLabel      minimal number of label occurences [0]
  -wordNgrams         max length of word ngram [1]
  -bucket             number of buckets [2000000]
  -minn               min length of char ngram [0]
  -maxn               max length of char ngram [0]
  -t                  sampling threshold [0.0001]
  -label              labels prefix [__label__]

The following arguments for training are optional:
  -lr                 learning rate [0.1]
  -lrUpdateRate       change the rate of updates for the learning rate [100]
  -dim                size of word vectors [100]
  -ws                 size of the context window [5]
  -epoch              number of epochs [5]
  -neg                number of negatives sampled [5]
  -loss               loss function {ns, hs, softmax} [softmax]
  -thread             number of threads [12]
  -pretrainedVectors  pretrained word vectors for supervised learning []
  -saveOutput         whether output params should be saved [false]

The following arguments for quantization are optional:
  -cutoff             number of words and ngrams to retain [0]
  -retrain            whether embeddings are finetuned if a cutoff is applied [false]
  -qnorm              whether the norm is quantized separately [false]
  -qout               whether the classifier is quantized [false]
  -dsub               size of each sub-vector [2]
```

#### 训练

默认ngram=1,一行一个数据，标签使用前缀`__label__`得到。输出的模型有两个，一个是.bin文件，一个是.vec文件，.bin为受过训练的分类模型，.vec是词的向量表示, one per line.

```shell
$ ./fasttext supervised -input train.txt -output model
```

#### 预测

```shell
$ ./fasttext predict model.bin test.txt k
```

#### 概率预测

```shell
$ ./fasttext predict-prob model.bin test.txt k
```

#### 测试

**注意：**得到的为precision和recall为各类平均

```shell
$ ./fasttext test model.bin test.txt 1


usage: fasttext test <model> <test-data> [<k>] [<th>]

  <model>      model filename
  <test-data>  test data filename (if -, read from stdin)
  <k>          (optional; 1 by default) top k个标签中有正确的，正确则1/k,错误为0
  <th>         (optional; 0.0 by default) 概率阈值
```

### 词嵌入

skipgram和cbow参数一样

```shell
The following arguments are mandatory:
  -input              training file path
  -output             output file path

The following arguments are optional:
  -verbose            verbosity level [2]

The following arguments for the dictionary are optional:
  -minCount           minimal number of word occurences [5]
  -minCountLabel      minimal number of label occurences [0]
  -wordNgrams         max length of word ngram [1]
  -bucket             number of buckets [2000000]
  -minn               min length of char ngram [3]
  -maxn               max length of char ngram [6]
  -t                  sampling threshold [0.0001]
  -label              labels prefix [__label__]

The following arguments for training are optional:
  -lr                 learning rate [0.05]
  -lrUpdateRate       change the rate of updates for the learning rate [100]
  -dim                size of word vectors [100]
  -ws                 size of the context window [5]
  -epoch              number of epochs [5]
  -neg                number of negatives sampled [5]
  -loss               loss function {ns, hs, softmax} [ns]
  -thread             number of threads [12]
  -pretrainedVectors  pretrained word vectors for supervised learning []
  -saveOutput         whether output params should be saved [false]

The following arguments for quantization are optional:
  -cutoff             number of words and ngrams to retain [0]
  -retrain            whether embeddings are finetuned if a cutoff is applied [false]
  -qnorm              whether the norm is quantized separately [false]
  -qout               whether the classifier is quantized [false]
  -dsub               size of each sub-vector [2]
```

#### 训练

默认为100维的向量空间，dsub默认为2（词分解为2）。数据为无header的正常英文文本，一行一个文本。输出为两个模型，一个.bin，一个.vec。fil9.bin文件是存储整个fastText模型的二进制文件，可以随后加载。 fil9.vec文件是包含单词向量的文本文件，词汇表中的每个单词每行一个：

```shell
$ ./fasttext skipgram -input data/fil9 -output result/fil9
```

#### 展示词向量

```shell
#展示queries.txt的词向量
$ ./fasttext print-word-vectors model.bin < queries.txt
#展示'enviroment'的向量
$ echo "enviroment" | ./fasttext print-word-vectors result/fil9.bin
```

#### 最近邻查询

```shell
#result/fil9.bin 为模型位置
$ ./fasttext nn result/fil9.bin
```

#### 词类比

```shell
$ ./fasttext analogies result/fil9.bin
Pre-computing word vectors... done.
Query triplet (A - B + C)? berlin germany france
paris 0.896462
bourges 0.768954
louveciennes 0.765569
toulouse 0.761916
valenciennes 0.760251
montpellier 0.752747
strasbourg 0.744487
meudon 0.74143
bordeaux 0.740635
pigneaux 0.736122
```

## [python使用](https://github.com/salestock/fastText.py)

### Requirements

fasttext support Python 2.6 or newer. It requires [Cython](https://pypi.python.org/pypi/Cython/) in order to build the C++ extension.

### Installation

```
pip install fasttext
```

### Word representation learning

In order to learn word vectors, as described in [1](https://github.com/salestock/fastText.py#enriching-word-vectors-with-subword-information), we can use `fasttext.skipgram` and `fasttext.cbow` function like the following:

```
import fasttext

# Skipgram model
model = fasttext.skipgram('data.txt', 'model')
print model.words # list of words in dictionary

# CBOW model
model = fasttext.cbow('data.txt', 'model')
print model.words # list of words in dictionary
```

where `data.txt` is a training file containing `utf-8` encoded text. By default the word vectors will take into account character n-grams from 3 to 6 characters.

At the end of optimization the program will save two files: `model.bin` and `model.vec`.

`model.vec` is a text file containing the word vectors, one per line. `model.bin` is a binary file containing the parameters of the model along with the dictionary and all hyper parameters.

The binary file can be used later to compute word vectors or to restart the optimization.

The following `fasttext(1)` command is equivalent

```
# Skipgram model
./fasttext skipgram -input data.txt -output model

# CBOW model
./fasttext cbow -input data.txt -output model
```

### Obtaining word vectors for out-of-vocabulary words

The previously trained model can be used to compute word vectors for out-of-vocabulary words.

```
print model['king'] # get the vector of the word 'king'
```

the following `fasttext(1)` command is equivalent:

```
echo "king" | ./fasttext print-vectors model.bin
```

This will output the vector of word `king` to the standard output.

### Load pre-trained model

We can use `fasttext.load_model` to load pre-trained model:

```
model = fasttext.load_model('model.bin')
print model.words # list of words in dictionary
print model['king'] # get the vector of the word 'king'
```

### Text classification

This package can also be used to train supervised text classifiers and load pre-trained classifier from fastText.

In order to train a text classifier using the method described in [2](https://github.com/salestock/fastText.py#bag-of-tricks-for-efficient-text-classification), we can use the following function:

```
classifier = fasttext.supervised('data.train.txt', 'model')
```

equivalent as `fasttext(1)` command:

```
./fasttext supervised -input data.train.txt -output model
```

where `data.train.txt` is a text file containing a training sentence per line along with the labels. By default, we assume that labels are words that are prefixed by the string `__label__`.

We can specify the label prefix with the `label_prefix` param:

```
classifier = fasttext.supervised('data.train.txt', 'model', label_prefix='__label__')
```

equivalent as `fasttext(1)` command:

```
./fasttext supervised -input data.train.txt -output model -label '__label__'
```

This will output two files: `model.bin` and `model.vec`.

Once the model was trained, we can evaluate it by computing the precision at 1 (P@1) and the recall on a test set using `classifier.test` function:

```
result = classifier.test('test.txt')
print 'P@1:', result.precision
print 'R@1:', result.recall
print 'Number of examples:', result.nexamples
```

This will print the same output to stdout as:

```
./fasttext test model.bin test.txt
```

In order to obtain the most likely label for a list of text, we can use `classifer.predict` method:

```
texts = ['example very long text 1', 'example very longtext 2']
labels = classifier.predict(texts)
print labels

# Or with the probability
labels = classifier.predict_proba(texts)
print labels
```

We can specify `k` value to get the k-best labels from classifier:

```
labels = classifier.predict(texts, k=3)
print labels

# Or with the probability
labels = classifier.predict_proba(texts, k=3)
print labels
```

This interface is equivalent as `fasttext(1)` predict command. The same model with the same input set will have the same prediction.

### API documentation

#### Skipgram model

Train & load skipgram model

```
model = fasttext.skipgram(params)
```

List of available `params` and their default value:

```
input_file     training file path (required)
output         output file path (required)
lr             learning rate [0.05]
lr_update_rate change the rate of updates for the learning rate [100]
dim            size of word vectors [100]
ws             size of the context window [5]
epoch          number of epochs [5]
min_count      minimal number of word occurences [5]
neg            number of negatives sampled [5]
word_ngrams    max length of word ngram [1]
loss           loss function {ns, hs, softmax} [ns]
bucket         number of buckets [2000000]
minn           min length of char ngram [3]
maxn           max length of char ngram [6]
thread         number of threads [12]
t              sampling threshold [0.0001]
silent         disable the log output from the C++ extension [1]
encoding       specify input_file encoding [utf-8]


```

Example usage:

```
model = fasttext.skipgram('train.txt', 'model', lr=0.1, dim=300)
```

#### CBOW model

Train & load CBOW model

```
model = fasttext.cbow(params)
```

List of available `params` and their default value:

```
input_file     training file path (required)
output         output file path (required)
lr             learning rate [0.05]
lr_update_rate change the rate of updates for the learning rate [100]
dim            size of word vectors [100]
ws             size of the context window [5]
epoch          number of epochs [5]
min_count      minimal number of word occurences [5]
neg            number of negatives sampled [5]
word_ngrams    max length of word ngram [1]
loss           loss function {ns, hs, softmax} [ns]
bucket         number of buckets [2000000]
minn           min length of char ngram [3]
maxn           max length of char ngram [6]
thread         number of threads [12]
t              sampling threshold [0.0001]
silent         disable the log output from the C++ extension [1]
encoding       specify input_file encoding [utf-8]


```

Example usage:

```
model = fasttext.cbow('train.txt', 'model', lr=0.1, dim=300)
```

#### Load pre-trained model

File `.bin` that previously trained or generated by fastText can be loaded using this function

```
model = fasttext.load_model('model.bin', encoding='utf-8')
```

#### Attributes and methods for the model

Skipgram and CBOW model have the following atributes & methods

```
model.model_name       # Model name
model.words            # List of words in the dictionary
model.dim              # Size of word vector
model.ws               # Size of context window
model.epoch            # Number of epochs
model.min_count        # Minimal number of word occurences
model.neg              # Number of negative sampled
model.word_ngrams      # Max length of word ngram
model.loss_name        # Loss function name
model.bucket           # Number of buckets
model.minn             # Min length of char ngram
model.maxn             # Max length of char ngram
model.lr_update_rate   # Rate of updates for the learning rate
model.t                # Value of sampling threshold
model.encoding         # Encoding of the model
model[word]            # Get the vector of specified word
```

#### Supervised model

Train & load the classifier

```
classifier = fasttext.supervised(params)
```

List of available `params` and their default value:

```
input_file     			training file path (required)
output         			output file path (required)
label_prefix   			label prefix ['__label__']
lr             			learning rate [0.1]
lr_update_rate 			change the rate of updates for the learning rate [100]
dim            			size of word vectors [100]
ws             			size of the context window [5]
epoch          			number of epochs [5]
min_count      			minimal number of word occurences [1]
neg            			number of negatives sampled [5]
word_ngrams    			max length of word ngram [1]
loss           			loss function {ns, hs, softmax} [softmax]
bucket         			number of buckets [0]
minn           			min length of char ngram [0]
maxn           			max length of char ngram [0]
thread         			number of threads [12]
t              			sampling threshold [0.0001]
silent         			disable the log output from the C++ extension [1]
encoding       			specify input_file encoding [utf-8]
pretrained_vectors		pretrained word vectors (.vec file) for supervised learning []


```

Example usage:

```
classifier = fasttext.supervised('train.txt', 'model', label_prefix='__myprefix__',
                                 thread=4)
```

#### Load pre-trained classifier

File `.bin` that previously trained or generated by fastText can be loaded using this function.

```
./fasttext supervised -input train.txt -output classifier -label 'some_prefix'
```

```
classifier = fasttext.load_model('classifier.bin', label_prefix='some_prefix')
```

#### Test classifier

This is equivalent as `fasttext(1)` test command. The test using the same model and test set will produce the same value for the precision at one and the number of examples.

```
result = classifier.test(params)

# Properties
result.precision # Precision at one
result.recall    # Recall at one
result.nexamples # Number of test examples
```

The param `k` is optional, and equal to `1` by default.

#### Predict the most-likely label of texts

This interface is equivalent as `fasttext(1)` predict command.

`texts` is an array of string

```
labels = classifier.predict(texts, k)

# Or with probability
labels = classifier.predict_proba(texts, k)
```

The param `k` is optional, and equal to `1` by default.

#### Attributes and methods for the classifier

Classifier have the following atributes & methods

```
classifier.labels                  # List of labels
classifier.label_prefix            # Prefix of the label
classifier.dim                     # Size of word vector
classifier.ws                      # Size of context window
classifier.epoch                   # Number of epochs
classifier.min_count               # Minimal number of word occurences
classifier.neg                     # Number of negative sampled
classifier.word_ngrams             # Max length of word ngram
classifier.loss_name               # Loss function name
classifier.bucket                  # Number of buckets
classifier.minn                    # Min length of char ngram
classifier.maxn                    # Max length of char ngram
classifier.lr_update_rate          # Rate of updates for the learning rate
classifier.t                       # Value of sampling threshold
classifier.encoding                # Encoding that used by classifier
classifier.test(filename, k)       # Test the classifier
classifier.predict(texts, k)       # Predict the most likely label
classifier.predict_proba(texts, k) # Predict the most likely label include their probability

```

The param `k` for `classifier.test`, `classifier.predict` and `classifier.predict_proba` is optional, and equal to `1` by default.