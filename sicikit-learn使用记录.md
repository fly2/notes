# sicikit-learn使用记录

## 常用算法

### Nearest Neighbors 

 最近邻算法可以用来做无监督的聚类，也可以做有监督的分类和回归。

聚类即kmeans算法，计算点到点的距离，找出最近的聚类中心点。

分类/回归则是考虑邻居的情况，根据最近的n个邻居（或在一定范围内的所有邻居）的类别（数值）来得到自己的类别（数值）。

#### 聚类

kmeans++算法：

初始随机选取第一个聚类中心，之后计算数据集中的所有点（或降低计算量随机选取n个点）到最近的聚类中心的距离$d_x$，使用轮盘赌算法得到下一个聚类中心，被选取概率为到最近聚类中心的距离$d_x$。当完成初始聚类中心选取后，使用标准kmeans算法进行聚类计算。

elkan K-Means算法：

利用三角形性质（两边之和大于第三边，两边之差小于第三边）来减少计算量。不适用于稀疏矩阵，由于数值缺失导致某些距离无法计算。

##### kmeans聚类

```python
#kmeans聚类
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

##### MiniBatchKMeans聚类

MinibatchKmeans聚类相比正常的kmeans聚类，每次只使用部分数据去更新聚类簇心。相对于使用全部数据去更新聚类簇心，会损失一些精度，但会提高速度。适用于数据量很大时使用。使用fit_predict可能后缺少部分分类的数据点。

平滑惯性（ewa_inertia）定义为：

alpha=batch_size*2/(n_samples+1)

 ewa_inertia = ewa_inertia * (1 - alpha) + batch_inertia * alpha

batch_size为批次的大小。n_samples为样本点数目，+1为了防止除数为0导致无效。batch_inertia为样本距离最近的聚类中心的总和。第一次ewa_inertia = batch_inertia，之后的平滑惯性由上面的式子计算得出。

```python
MiniBatchKMeans(n_clusters=8, init='k-means++', max_iter=100, batch_size=100, verbose=0, compute_labels=True, random_state=None, tol=0.0, max_no_improvement=10, init_size=None, n_init=3, reassignment_ratio=0.01)
#n_clusters 初始化的质心数目
#init 初始化质心，可以是方法，可以是质心矩阵。如果是矩阵，则尺寸应为[n_clusters, n_features]
#k-means++:初始的聚类中心之间的相互距离要尽可能的远。
#random:从数据中随机选取初始聚类中心
#max_iter 最大迭代次数
#batch_size 批次大小，每次更新质心所用的数据量
#verbose 是否输出详细信息
#compute_labels 在minibatch收敛后为整个数据集的数据加上标签，默认为True
#random_state 为随机数生成器设置种子
#tol 通过聚类中心的相对位移变化来提前停止聚类，默认为0。中心相对位移变化通过平滑，方差归一化后计算中心距离平方均值测量。
#max_no_improvement 最大连续未降低平滑惯性的次数，当达到最大次数，则停止算法。默认为10.如果要禁止此提前停止条件，设置max_no_improvement=None
#init_size 初始化中心点时所需要的随机采样数量。默认为3倍的batch_size。
#n_init 尝试的随机初始化次数。
#reassignment_ratio 控制要重新分配的中心的最大计数数量的分数。 较高的值意味着低计数中心更容易重新分配，这意味着模型将需要更长的时间来收敛，可能产生更好的聚类效果。
```



#### 分类/回归

最近邻有两种方法，一种是根据最近的邻居表现做分类（回归），一种是根据一定范围半径r内的邻居的表现做分类(回归)。  
  在用KNeighborsRegressor或RadiusNeighborsRegressor做回归，即对近邻的值做平均`weights = 'uniform'`,也可以按照距离加权平均`weights = 'distance'`。**在scikit-learn中distance加权是按照距离倒数作加权**

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

#knn分类
KNeighborsClassifier(n_neighbors=5, weights='uniform', algorithm='auto', leaf_size=30, p=2, metric='minkowski', metric_params=None, n_jobs=1, **kwargs)
#n_neighbors 考虑的最近邻个数
#weights 预测时的权重函数{'uniform','distance'}.'uniform'为均值平均，'distance'加权平均，权重为距离的倒数。我们也可以自定义权重，即自定义一个函数，输入是距离值，输出是权重值。这样我们可以自己控制不同的距离所对应的权重。
#algorithm 计算最近邻时使用的算法{‘auto’, ‘ball_tree’, ‘kd_tree’, ‘brute’}。当输入是稀疏矩阵时，会无视此参数使用暴力计算{'brute'}的方法。
#'brute':暴力计算，无任何优化
#'ball_tree':适用于高维数据，在kd-tree能够处理的数据范围内要慢于kd-tree。
#'kd_tree':适用于低维数据，维数小于20时效率最高
#leaf_size 叶的尺寸，停止建子树的叶子节点数量的阈值，不影响结果。只影响内存大小和查询结果的速度。leaf_size一般leaf_size<n_samples<2*leaf_size,除非n_samples<leaf_size。leaf_size越大速度越快。
#p 闵可夫斯基距离的p值，1时为曼哈顿距离，2时为欧式距离，无穷时为切比雪夫距离。
#metric 字符串或距离矩阵对象。默认为闵可夫斯基距离（'minkowski'）
#metric_params 度量函数的附加关键字参数。主要是用于带权重闵可夫斯基距离的权重，以及其他一些比较复杂的距离度量的参数。
#n_joibs 并行线程数。如果为-1，则设置为cpu的核心数。

#rnn分类
RadiusNeighborsClassifier(radius=1.0, weights='uniform', algorithm='auto', leaf_size=30, p=2, metric='minkowski', outlier_label=None, metric_params=None, **kwargs)
#radius 使用的参数空间范围，即近邻判定的半径。在半径内的即为近邻。
#weights 预测时的权重函数{'uniform','distance'}.'uniform'为均值平均，'distance'加权平均，权重为距离的倒数。我们也可以自定义权重，即自定义一个函数，输入是距离值，输出是权重值。这样我们可以自己控制不同的距离所对应的权重。
#algorithm 计算最近邻时使用的算法{‘auto’, ‘ball_tree’, ‘kd_tree’, ‘brute’}。当输入是稀疏矩阵时，会无视此参数使用暴力计算{'brute'}的方法。
#'brute':暴力计算，无任何优化
#'ball_tree':适用于高维数据，在kd-tree能够处理的数据范围内要慢于kd-tree。
#'kd_tree':适用于低维数据，维数小于20时效率最高
#leaf_size 叶的尺寸，停止建子树的叶子节点数量的阈值，不影响结果。只影响内存大小和查询结果的速度。leaf_size一般leaf_size<n_samples<2*leaf_size,除非n_samples<leaf_size。leaf_size越大速度越快。
#p 闵可夫斯基距离的p值，1时为曼哈顿距离，2时为欧式距离，无穷时为切比雪夫距离。
#metric 字符串或距离矩阵对象。默认为闵可夫斯基距离（'minkowski'）
#metric_params 度量函数的附加关键字参数。主要是用于带权重闵可夫斯基距离的权重，以及其他一些比较复杂的距离度量的参数。
#outlier_label 主要用于预测时，如果目标点半径内没有任何训练集的样本点时，应该标记的类别，不建议选择默认值 none,因为这样遇到异常点会报错。可设置为训练集没有的类别以表明异常。
```

### Logistic Model

#### 分类

```python
LogisticRegression(penalty='l2', dual=False, tol=0.0001, C=1.0, fit_intercept=True, intercept_scaling=1, class_weight=None, random_state=None, solver='liblinear', max_iter=100, multi_class='ovr', verbose=0, warm_start=False, n_jobs=1)
#penalty{'l1','l2'} 指定的用于惩罚过拟合的范数。‘newton-cg’,‘sag’和‘lbfgs’只支持'l2'范数
#dual 是否计算其对偶形式。（svm中用到对偶问题）如果计算对偶形式，只能使用l2范数惩罚。当n_sample>n_feature时，建议dual=False
#tol 停止的标准，当优化器（newton-cg and lbfgs）的提升小于tol则停止
#C 正则化力量的倒数。必须是一个正的浮点型数字。像支持向量机一样，更小的值表示更强的正则化。
#fit_intercept 指定是否将一个常量（偏差或截距）添加到决策函数中
#intercept_scaling 只当使用'liblinear'求解，且fit_intercept=True时有用。在这种情况下，x变为[x,self.intercept_scaling]，即将等于intercept_scaling的常量作为“合成”特征附加到实例向量。 截距成为intercept_scaling * synthetic_feature_weight。 注意!合成特征和其他所有特征一样需要进行l1/l2正则。为了减少正则化对合成特征权重的影响（在截距上），必须增大intercept_scaling。
#class_weight 可输入字典或者'balanced'。字典格式为{class_label:weight},如果不提供，所有的分类权重为1.如果设置为'balanced'模式，则会自动根据输入的数据集中各类别出现的频数的反比作为权重，具体公式为weight_i=n_samples / (n_classes * n_class_i) n_classes为类别数,n_class_i为输入数据集中分类为i类别的数目。请注意，如果指定了sample_weight，这些权重将乘以sample_weight（通过fit方法传递）。
#random_state 随机数生成器种子，仅用于'sag'和'liblinear'求解。
#solver {‘newton-cg’, ‘lbfgs’, ‘liblinear’, ‘sag’}用来优化问题的算法。
对于小数据集，用'liblinear'是一个好的选择，当数据集更大时，使用'sag'会得到更快的速度。
对于多类问题，只有'newton-cg', 'sag' 和 'lbfgs'可以处理多项损失，'liblinear'只能用来处理'one-vs-rest'的情况
'newton-cg'，'lbfgs'和'sag'只处理L2惩罚。
请注意，'sag'快速收敛仅在具有大致相同尺度的特征上得到保证。可以使用sklearn.preprocessing中的缩放器预处理数据。
#max_iter solver的最大迭代次数，只对'newton-cg', 'sag' and 'lbfgs'有用
#multi_class {'ovr', 'multinomial'},多分类选项。如果选择'ovr'（one-vs-rest），则将演变成n个二分类问题，每个分类将自己作为正例，其他分类作为负例进行分类训练。选择'multinomial'，则是最小化多分类损失函数，此选项只适用于'newton-cg'，'sag'和'lbfgs'。
#verbose 对于'liblinear' 和 'lbfgs' 求解器(solver)，可以设置为任意正数来输出详细信息。
#warm_start 当设置为True时，重新使用上一次调用的解决方案作为初始化，否则，擦除以前的解决方案。 对于liblinear求解器无用。
#n_jobs 交叉验证循环期间使用的CPU内核数。 如果值为-1，则使用所有核心。
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

