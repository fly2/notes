# sicikit-learn使用记录

## 常用算法

### Nearest Neighbors 

  最近邻有两种方法，一种是根据最近的邻居表现做分类（回归），一种是根据一定范围半径r内的邻居的表现做分类(回归)。  
  在用KNeighborsRegressor或RadiusNeighborsRegressor做回归，即对近邻的值做平均`weights = 'uniform'`,也可以按照距离加权平均`weights = 'distance'`。**在scikit-learn中distance加权是按照距离倒数作加权**



#### 聚类

kmeans++算法：

初始随机选取第一个聚类中心，之后计算数据集中的所有点（或降低计算量随机选取n个点）到最近的聚类中心的距离$d_x$，使用轮盘赌算法得到下一个聚类中心，被选取概率为到最近聚类中心的距离$d_x$。当完成初始聚类中心选取后，使用标准kmeans算法进行聚类计算。

elkan K-Means算法：

利用三角形性质（两边之和大于第三边，两边之差小于第三边）来减少计算量。不适用于稀疏矩阵，由于数值缺失导致某些距离无法计算。

```python
KMeans(n_clusters=8, init='k-means++', n_init=10, max_iter=300, tol=0.0001, precompute_distances='auto', verbose=0, random_state=None, copy_x=True, n_jobs=1, algorithm='auto')
#n_clusters 生成的簇的数目
#init 初始化质心，可以是方法，可以是质心矩阵。如果是矩阵，则尺寸应为[n_clusters, n_features]
#k-means++:初始的聚类中心之间的相互距离要尽可能的远。
#random:从数据中随机选取初始聚类中心
#n_init设置选择质心种子的次数，默认为10次。返回质心最好的一次结果
#max_iter 每次运行的最大迭代次数
#tol 最小调整幅度阈值，当调整幅度小于该阈值则停止计算
#precompute_distances={'auto'，True,False} 内存和速度的权衡。如果是True 会把整个距离矩阵都放到内存中，auto 会默认在数据样本大于featurs*samples 的数量大于12e6 的时候False.
#verbose 是否输出详细信息
#random_state 生成聚类中心时使用的随机数的种子。
#copy_x 是否对输入数据继续copy操作，避免修改原有数据。默认为True
#n_jobs 使用进程数量
#algorithm{ “auto”, “full” or “elkan”} 使用的kmeans算法。自动在数据密集情况下使用'elkan'算法，稀疏数据使用'full'算法（即普通的kmeans算法）。

#聚类
from sklearn.cluster import KMeans
import numpy as np
#生成数据
X = np.array([[1, 2], [1, 4], [1, 0],[4, 2], [4, 4], [4, 0]])
kmeans = KMeans(n_clusters=2, random_state=0).fit(X)
#设置标签
kmeans.labels_=array([0, 0, 0, 1, 1, 1], dtype=int32)
#预测数据标签
kmeans.predict([[0, 0], [4, 4]])-->array([0, 1], dtype=int32)
#聚类中心点
kmeans.cluster_centers_-->array([[ 1.,  2.],[ 4.,  2.]])
```

#### 分类回归

#### balltree



```python
#找到最近的邻居
from sklearn.neighbors import NearestNeighbors
import numpy as np
X = np.array([[-1, -1], [-2, -1], [-3, -2], [1, 1], [2, 1], [3, 2]])
nbrs = NearestNeighbors(n_neighbors=2, algorithm='ball_tree').fit(X)
distances, indices = nbrs.kneighbors(X)
#最近的两个邻居index，包含自己
indices                                           
-->array([[0, 1],
       [1, 0],
       [2, 1],
       [3, 4],
       [4, 3],
       [5, 4]]...)
#最近两个邻居的距离，包含自己
distances
-->array([[ 0.        ,  1.        ],
       [ 0.        ,  1.        ],
       [ 0.        ,  1.41421356],
       [ 0.        ,  1.        ],
       [ 0.        ,  1.        ],
       [ 0.        ,  1.41421356]])

```

### gbdt

  GradientBoostingClassifier和GradientBoostingRegressor进行训练时，x需要为array-like格式(数组)，不能使用稀疏矩阵。当为稀疏矩阵时，可以考虑RandomForest或adaboost。

## 文本处理

### text

  scikit中自带有对文本处理的函数。

  `HashingVectorizer`输入为列表格式的文本，输出为onehot词矩阵，出现为1，未出现为0
  `CountVectorizer`输入为列表格式的文本，输出为词出现次数onehot矩阵

  `sklearn.feature_extraction.text.``CountVectorizer`(*input=u'content'*, *encoding=u'utf-8'*, *decode_error=u'strict'*, *strip_accents=None*, *lowercase=True*, *preprocessor=None*, *tokenizer=None*, *stop_words=None*, *token_pattern=u'(?u)\b\w\w+\b'*, *ngram_range=(1*, *1)*, *analyzer=u'word'*, *max_df=1.0*, *min_df=1*, *max_features=None*, *vocabulary=None*, *binary=False*, *dtype=<type 'numpy.int64'>*)

  `TfidfTransformer`输入为`CountVectorizer`的结果，输出为tf-idf，当只有一列时返回为tf即词频

  `TfidfVectorizer`输入为列表格式的文档，输出为tf-idf

## 模型验证

### cross-validation

  可以通过cross_val_score和cross_val_predict进行交叉验证。

```python
sklearn.model_selection.cross_val_score(estimator, X, y=None, groups=None, scoring=None, cv=None, n_jobs=1, verbose=0, fit_params=None, pre_dispatch='2*n_jobs')
#estimator用来训练数据的模型
#X输入的变量。不可使用稀疏矩阵
#y目标变量
#groups分类标签，将数据集分成测试集和训练集的分类标签，使样本抽样平衡。
#scoring 模型评价方法,可以输入下面的方法
#[‘accuracy‘, ‘adjusted_rand_score‘, ‘average_precision‘, ‘f1‘, ‘f1_macro‘, ‘f1_micro‘, ‘f1_samples‘, ‘f1_weighted‘, ‘log_loss‘, ‘mean_absolute_error‘, ‘mean_squared_error‘, ‘median_absolute_error‘, ‘precision‘, ‘precision_macro‘, ‘precision_micro‘, ‘precision_samples‘, ‘precision_weighted‘, ‘r2‘, ‘recall‘, ‘recall_macro‘, ‘recall_micro‘, ‘recall_samples‘, ‘recall_weighted‘, ‘roc_auc‘]
#cv 交叉验证的份数
#n_jobs 用来计算的cpu数目，-1使用所有的cpu


```

