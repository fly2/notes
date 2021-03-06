# scikit-learn使用记录

## 常用距离

### 余弦相似度

$$\Large  Cos_\theta=\frac{\sum_1^n(x_i y_i)}{\sqrt{\sum_1^n x_i^2}\sqrt{\sum_1^n y_i^2}}$$

```python
cosine_similarity(X, Y=None, dense_output=True)
```

### 余弦距离

$$\Large  1-Cos_\theta=1-\frac{\sum_1^n(x_i y_i)}{\sqrt{\sum_1^n x_i^2}\sqrt{\sum_1^n y_i^2}}$$

```python
cosine_distances(X, Y=None)
```

## 特征处理

### onehot编码

需要输入为int类型，且不能有负数。

```python
sklearn.preprocessing.OneHotEncoder(n_values=’auto’, categorical_features=’all’, dtype=<class ‘numpy.float64’>, sparse=True, handle_unknown=’error’)
#n_values 每个特征的值数量。有三种输入方式，{'auto',int,array}
'''
'auto'：从训练数据中确定数值范围。
int：每个要素的分类值数量。每个特征值应该在range(n_values)
array：n_values[i]是X[:,i]中的分类值的数量。每个特征的值数量应该在范围内（n_values [i]）
'''
#categorical_features 指定哪些特征被视为分类变量。有三种输入方式，{'all',array,mask}
'''
'all' 所有特征都被视为分类。
array 分类特征的索引的数组
mask 是否视为分类特征的True，False数组，长度为特征数目。
非分类特征总是堆叠在矩阵的右侧。
'''
#dtype 期望输出的数据类型
#sparse 如果设置为True则返回sparse矩阵，否则返回array
#handle_unknown {'error','ignore'}如果在变换过程中出现未知的分类特征，是提出报错还是忽略。
```

### minmax

将特征变化到指定范围内，下面为转换公式：

`X_std = (X - X.min(axis=0)) / (X.max(axis=0) - X.min(axis=0))`
`X_scaled = X_std * (max - min) + min`

```python
sklearn.preprocessing.MinMaxScaler(feature_range=(0, 1), copy=True)
#feature_range 指定变换到的范围，用tuple表示
#copy 当copy=False时，则直接在原数据上进行规范化操作。注意：当输入是一个np.array()时，此参数才有效，否则总是会新建np.array数据进行操作。

from sklearn.preprocessing import MinMaxScaler
data = [[-1, 2], [-0.5, 6], [0, 10], [1, 18]]
scaler = MinMaxScaler()
data=scaler.fit_transform(data)
```

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
#返回到每个中心的距离矩阵
kmeans.transform([[0, 0], [4, 4]])-->array([[ 2.23606798,  4.47213595],[ 3.60555128,  2.        ]])
```

##### MiniBatchKMeans聚类

MinibatchKmeans聚类相比正常的kmeans聚类，每次只使用部分数据去更新聚类簇心。相对于使用全部数据去更新聚类簇心，会损失一些精度，但会提高速度。适用于数据量很大时使用。

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
#'brute':暴力计算，无任何优化,时间复杂度O[DN]（D为维度，N为变量数）
#'ball_tree':适用于高维数据，在kd-tree能够处理的数据范围内要慢于kd-tree，时间复杂度约为O[Dlog(N)]
#'kd_tree':适用于低维数据，维数小于20时效率最高，D小于20的时间复杂度大约是O[Dlog(N)]
#leaf_size 叶的尺寸，停止建子树的叶子节点数量的阈值，不影响结果。只影响内存大小和查询结果的速度。leaf_size一般leaf_size<n_samples<2*leaf_size,除非n_samples<leaf_size。leaf_size越大速度越快。
#p 闵可夫斯基距离的p值，1时为曼哈顿距离，2时为欧式距离，无穷时为切比雪夫距离。
#metric 字符串或距离矩阵对象。默认为闵可夫斯基距离（'minkowski'）
#metric_params 度量函数的附加关键字参数。主要是用于带权重闵可夫斯基距离的权重，以及其他一些比较复杂的距离度量的参数。
#outlier_label 主要用于预测时，如果目标点半径内没有任何训练集的样本点时，应该标记的类别，不建议选择默认值 none,因为这样遇到异常点会报错。可设置为训练集没有的类别以表明异常。
```

##### knn算法选择

**KD树**

为了解决效率低下的暴力计算方法，已经发明了大量的基于树的数据结构。总的来说， 这些结构试图通过有效地编码样本的 aggregate distance (聚合距离) 信息来减少所需的距离计算量。 基本思想是，若A点距离B点非常远，B点距离C点非常近， 可知点与C点很遥远，*不需要明确计算它们的距离*。 通过这样的方式，近邻搜索的计算成本可以降低为O[DNlog(N)]或更低。 这是对于暴力搜索在大样本数N中表现的显著改善。

利用这种聚合信息的早期方法是 *KD tree* 数据结构（* K-dimensional tree* 的简写）, 它将二维 *Quad-trees* 和三维 *Oct-trees*推广到任意数量的维度. KD 树是一个二叉树结构，它沿着数据轴递归地划分参数空间，将其划分为嵌入数据点的嵌套的各向异性区域。 KD 树的构造非常快：因为只需沿数据轴执行分区, 无需计算D-dimensional 距离。 一旦构建完成, 查询点的最近邻距离计算复杂度仅为O[log(N)]。 虽然 KD 树的方法对于低维度 (D<20) 近邻搜索非常快, 当D增长到很大时, 效率变低: 这就是所谓的 “维度灾难” 的一种体现。 

------

**ball树**

为了解决 KD 树在高维上效率低下的问题, *ball 树* 数据结构就被研发出来了. 其中 KD 树沿卡迪尔轴（即坐标轴）分割数据, ball 树在沿着一系列的 hyper-spheres 来分割数据. 通过这种方法构建的树要比 KD 树消耗更多的时间, 但是这种数据结构对于高结构化的数据是非常有效的, 即使在高维度上也是一样.

ball 树将数据递归地划分为由质心 C和半径r定义的节点,使得节点中的每个点位于由r和C

定义的 hyper-sphere 内. 通过使用 *triangle inequality（三角不等式）* 减少近邻搜索的候选点数:|x+y|<=|x|+|y|通过这种设置, 测试点和质心之间的单一距离计算足以确定距节点内所有点的距离的下限和上限. 由于 ball 树节点的球形几何, 它在高维度上的性能超出 *KD-tree*, 尽管实际的性能高度依赖于训练数据的结构。

------

**最近邻算法的选择**

对于给定数据集的最优算法是一个复杂的选择, 并且取决于多个因素:

- 样本数量 N(i.e. `n_samples`) 和维度(例如. `n_features`).
- *Brute force* 查询时间以 O[DN]增长
- *Ball tree* 查询时间大约以 O[Dlog(N)]增长
- *KD tree* 的查询时间 D的变化是很难精确描述的.对于较小的D(小于20) 的成本大约是O[Dlog(N)], 并且 KD 树更加有效.对于较大的D成本的增加接近O[DN], 由于树结构引起的开销会导致查询效率比暴力还要低.

对于小数据集 (N小于30),log(N)相当于N, 暴力算法比基于树的算法更加有效.`KDTree` 和 `BallTree` 通过提供一个 *leaf size* 参数来解决这个问题:这控制了查询切换到暴力计算样本数量. 使得两种算法的效率都能接近于对较小的N的暴力计算的效率.

- 数据结构: 数据的 *intrinsic dimensionality* (本征维数) 和/或数据的 *sparsity* (稀疏度). 本征维数是指数据所在的流形的维数d<=D, 在参数空间可以是线性或非线性的. 稀疏度指的是数据填充参数空间的程度(这与“稀疏”矩阵中使用的概念不同, 数据矩阵可能没有零项, 但是从这个意义上来讲,它的 **structure** 仍然是 “稀疏” 的)。
- *Brute force* (暴力查询)时间不受数据结构的影响。
- *Ball tree* 和 *KD tree* 的数据结构对查询时间影响很大. 一般地, 小维度的 sparser (稀疏) 数据会使查询更快. 因为 KD 树的内部表现形式是与参数轴对齐的, 对于任意的结构化数据它通常不会表现的像 ball tree 那样好.

在机器学习中往往使用的数据集是非常结构化的, 而且非常适合基于树结构的查询。

- query point（查询点）所需的近邻数k。
- *Brute force* 查询时间几乎不受k值的影响.
- *Ball tree* 和 *KD tree* 的查询时间会随着k的增加而变慢. 这是由于两个影响: 首先, k的值越大在参数空间中搜索的部分就越大. 其次, 使用k>1进行树的遍历时, 需要对内部结果进行排序.

当k相比N变大时, 在基于树的查询中修剪树枝的能力是减弱的. 在这种情况下, 暴力查询会更加有效.

- query points（查询点）数. ball tree 和 KD Tree 都需要一个构建阶段. 在许多查询中分摊时，这种结构的成本可以忽略不计。 如果只执行少量的查询, 可是构建成本却占总成本的很大一部分. 如果仅需查询很少的点, 暴力方法会比基于树的方法更好.

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

### DBSCAN

是一个比较有代表性的基于密度的[聚类算法](https://baike.baidu.com/item/%E8%81%9A%E7%B1%BB%E7%AE%97%E6%B3%95)。与划分和层次聚类方法不同，它将簇定义为密度相连的点的最大集合，能够把具有足够高密度的区域划分为簇，并可在噪声的[空间数据库](https://baike.baidu.com/item/%E7%A9%BA%E9%97%B4%E6%95%B0%E6%8D%AE%E5%BA%93)中发现任意形状的聚类。

#### 聚类算法

```python
DBSCAN(eps=0.5, min_samples=5, metric='euclidean', metric_params=None, algorithm='auto', leaf_size=30, p=None, n_jobs=1)
#eps 两个样本被认为在同一个社区的最大距离。
#min_samples 作为核心点的点的邻域中的样本数（或总权重）。 包括核心点本身。
#metric 计算特征数组中实例之间的距离时使用的度量。 如果度量标准是字符串或可调用，则它必须是其度量参数由metrics.pairwise.calculate_distance允许的选项之一。 如果度量是“预先计算的”，则假设X是距离矩阵，并且必须是平方。 X可以是稀疏矩阵，在这种情况下，只有“非零”元素可以被认为是DBSCAN的邻居。
#metric_params 为距离度量添加关键字参数
#algorithm  {'auto', 'ball_tree', 'kd_tree', 'brute'}由NearestNeighbors模块用于计算点距离并找到最近邻居的算法。 有关详细信息，请参阅NearestNeighbors模块文档。
#leaf_size 叶子尺寸传递给BallTree或KDTree。 这可能会影响构建和查询的速度，以及存储树所需的内存。 最优值取决于问题的性质。
#p 用于计算点之间的距离的闵可夫斯基度量的权力。
#n_jobs 并行运行数目。
```

### EllipticEnvelope

#### 异常值检测

```python
EllipticEnvelope(store_precision=True, assume_centered=False, support_fraction=None, contamination=0.1, random_state=None)
#store_precision 指定是否存储估计精度。
#assume_centered 如果为True，则计算鲁棒位置和协方差估计的支持，并且从其重新计算协方差估计，而不用数据中心。 用于处理平均值大大等于零但不完全为零的数据。 如果False，鲁棒的位置和协方差直接用FastMCD算法计算，无需额外的处理。
#support_fraction 要支持原始MCD估计的要点的比例。 默认值为None，这意味着在算法中将使用support_fraction的最小值：[n_sample + n_features + 1] / 2。
#contamination 数据集的污染量，即数据集中异常值的比例。
#random_state 随机种子

from sklearn.covariance import EllipticEnvelope
#EllipticEnvelope
ee=EllipticEnvelope(store_precision=True, assume_centered=False, support_fraction=None, contamination=0.003, random_state=0)
ee=ee.fit(vec1)
py=ee.predict(vec2)
precision=sum(test_y[py==1])/sum(py==1)
recall=sum(py[np.array(test_y==1)])/sum(test_y==1)
f1=f1_score(py,test_y)
res.loc[r,:]=[100,precision,recall,f1]
r=r+1
```

### 距离度量

[距离划分度量](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.distance_metrics.html#sklearn.metrics.pairwise.distance_metrics)

## 降维算法

### t-sne

t分布随机相邻嵌入。
t-SNE [1]是可视化高维数据的工具。 它将数据点之间的相似性转换为联合概率，并尝试最小化低维嵌入与高维数据的联合概率之间的Kullback-Leibler分歧。 t-SNE具有不是凸的成本函数，即不同的初始化，我们可以得到不同的结果。
强烈建议使用另外的维数降低方法（例如密度数据的PCA或稀疏数据的TruncatedSVD），如果特征数量非常多，则将维数减少到合理的数量（例如50）。 这将抑制一些噪音，并加快样品之间成对距离的计算。 有关更多提示，请参阅Laurens van der Maaten的常见问题[2]。

```python
TSNE(n_components=2, perplexity=30.0, early_exaggeration=12.0, learning_rate=200.0, n_iter=1000, n_iter_without_progress=300, min_grad_norm=1e-07, metric='euclidean', init='random', verbose=0, random_state=None, method='barnes_hut', angle=0.5)
#n_components 嵌入的空间维数，为int类型
#perplexity 这种perplexity和其他流形学习算法中使用的最近邻数有关。更大的数据集通常需要更大的perplexity。考虑在5到50之间选择一个值。选择不是非常重要因为T-SNE对这个参数非常不敏感。
#early_exaggeration 控制原始空间中的自然簇在嵌入空间中的紧密程度，以及它们之间有多少空间。对于较大的值，在嵌入空间中自然簇之间的空间将更大。同样，这个参数的选择不是很关键。如果成本函数在初始优化期间增加，则早期夸张因子或学习率可能太高
#learning_rate 对于T-SNE学习速度通常在范围[ 10，1000 ]。如果学习率太高，数据看起来像一个“球”，其点与最近的邻居大致相等。如果学习率太低，大多数点看起来像是在稠密的云中压缩，而离群值很少。如果成本函数陷入一个糟糕的局部极小值，增加学习率可能会有所帮助。
#n_iter 优化的最大迭代次数，应不小于250次
#n_iter_without_progress 在我们中止优化之前没有进展的最大迭代次数，在早期夸张的250次初始迭代之后使用。 请注意，只有每50次迭代检查进度，因此该值将四舍五入为50的下一个倍数。
#min_grad_norm 如果梯度范数低于该阈值，则优化将被停止。
#metric 计算特征数组中实例之间的距离时使用的度量。 如果度量标准是一个字符串，则它必须是其度量参数的scipy.spatial.distance.pdist允许的选项之一，或成对的.PAIRWISE_DISTANCE_FUNCTIONS中列出的度量标准。 如果度量是“预先计算的”，则X被假定为距离矩阵。 或者，如果度量是可调用函数，则在每对实例（行）上调用它，并记录结果值。 可调用应从X中取两个数组作为输入，并返回一个表示它们之间距离的值。 默认为“欧几里德”，它被解释为平方欧几里得距离。
#init 初始化嵌入。 可能的选项是'random'，'pca'和一个numpy数组（n_samples，n_components）。 PCA初始化不能与预计算距离一起使用，并且通常比随机初始化更全局稳定。
#verbose 详细程度。
#random_state 如果int，random_state是随机数生成器使用的种子; 如果RandomState的实例，random_state是随机数生成器; 如果None，则随机数生成器是由np.random使用的RandomState实例。 注意，不同的初始化可能导致成本函数的不同局部最小值。
#method 默认情况下，梯度计算算法使用在O（NlogN）时间运行的Barnes-Hut近似（method='barnes_hut'）。 method='exact'将运行较慢但精确的算法在O（N ^ 2）时间。 当最近邻错误需要优于3％时，应使用'exact'的算法。 然而，'exact'的方法不能扩展到数百万的例子。
#angle 只用于method ='barnes_hut'这是Barnes-Hut T-SNE的速度和精度之间的权衡。 “角度”是从一个点测量的远距离节点的角度大小（在[3]中称为θ）。 如果这个大小低于'angle'，那么它将被用作其中包含的所有点的汇总节点。 该方法对0.2〜0.8范围内的参数变化不敏感。 小于0.2的角具有很快的计算时间而大于0.8的角误差增大很快。




import numpy as np
from sklearn.manifold import TSNE
X = np.array([[0, 0, 0], [0, 1, 1], [1, 0, 1], [1, 1, 1]])
#利用fit_transform将x嵌入到低维空间，并返回x的低维嵌入
X_embedded = TSNE(n_components=2).fit_transform(X)
X_embedded.shape
```

## 集成算法

> 为什么用xgboost/gbdt在在调参的时候把树的最大深度调成6就有很高的精度了，但是用DecisionTree/RandomForest的时候需要把树的深度调到15或更高？

>一句话的解释，来自周志华老师的机器学习教科书（[ 机器学习-周志华](https://link.zhihu.com/?target=https%3A//www.amazon.cn/%25E6%259C%25BA%25E5%2599%25A8%25E5%25AD%25A6%25E4%25B9%25A0-%25E5%2591%25A8%25E5%25BF%2597%25E5%258D%258E/dp/B01ARKEV1G/ref%3Dsr_1_1%3Fie%3DUTF8%26qid%3D1462535564%26sr%3D8-1%26keywords%3D%25E6%259C%25BA%25E5%2599%25A8%25E5%25AD%25A6%25E4%25B9%25A0)）：Boosting主要关注降低偏差，因此Boosting能基于泛化性能相当弱的学习器构建出很强的集成；Bagging主要关注降低方差，因此它在不剪枝的决策树、神经网络等学习器上效用更为明显。
>
>随机森林(random forest)和GBDT都是属于集成学习（ensemble learning)的范畴。集成学习下有两个重要的策略Bagging和Boosting。
>
>Bagging算法是这样做的：每个分类器都随机从原样本中做有放回的采样，然后分别在这些采样后的样本上训练分类器，然后再把这些分类器组合起来。简单的多数投票一般就可以。其代表算法是随机森林。Boosting的意思是这样，他通过迭代地训练一系列的分类器，每个分类器采用的样本分布都和上一轮的学习结果有关。其代表算法是AdaBoost, GBDT。
>
>其实就机器学习算法来说，其泛化误差可以分解为两部分，偏差（bias)和方差(variance)。这个可由下图的式子导出（这里用到了概率论公式D(X)=E(X^2)-[E(X)]^2）。偏差指的是算法的期望预测与真实预测之间的偏差程度，反应了模型本身的拟合能力；方差度量了同等大小的训练集的变动导致学习性能的变化，刻画了数据扰动所导致的影响。这个有点儿绕，不过你一定知道过拟合。
>
>![bca1b7c915099702fd0e8a3a3e0b2cac_b](.\picture\bca1b7c915099702fd0e8a3a3e0b2cac_b.png)
>
>如下图所示，当模型越复杂时，拟合的程度就越高，模型的训练偏差就越小。但此时如果换一组数据可能模型的变化就会很大，即模型的方差很大。所以模型过于复杂的时候会导致过拟合。
>
>![1cca0e32949ab02127e56636b0ff080f_b](.\picture\1cca0e32949ab02127e56636b0ff080f_b.png)
>
>也就是说，当我们训练一个模型时，偏差和方差都得照顾到，漏掉一个都不行。
>对于Bagging算法来说，由于我们会并行地训练很多不同的分类器的目的就是降低这个方差(variance) $$\mathbf{E}[h-\mathbb{E}(h)]$$,因为采用了相互独立的基分类器多了以后，*h*的值自然就会靠近$$\mathbb{E}(h)$$.所以对于每个基分类器来说，目标就是如何降低这个偏差（bias),所以我们会采用深度很深甚至不剪枝的决策树。

### gbdt

  GradientBoostingClassifier和GradientBoostingRegressor进行训练时，x需要为array-like格式(数组)，不能使用稀疏矩阵。当为稀疏矩阵时，可以考虑RandomForest或adaboost。

#### 分类

```python
GradientBoostingClassifier(loss='deviance', learning_rate=0.1, n_estimators=100, subsample=1.0, criterion='friedman_mse', min_samples_split=2, min_samples_leaf=1, min_weight_fraction_leaf=0.0, max_depth=3, min_impurity_split=1e-07, init=None, random_state=None, max_features=None, verbose=0, max_leaf_nodes=None, warm_start=False, presort='auto')
#loss {‘deviance’, ‘exponential’}需要优化的损失函数。'deviance'指用概率（logistic回归）产出进行分类的偏差,'exponential'即等于adaboost算法
#learning_rate 通过该参数来缩小每棵树对学习率的贡献。较小的learning_rate意味着我们需要更多的弱学习器的n_estimators。通常我们用步长(learning_rate)和迭代最大次数(n_estimators)一起来决定算法的拟合效果。
#n_estimators 基决策树数目,即模型迭代次数
#subsample 子采样比例（subsample）。取值为(0,1]。注意这里的子采样和随机森林不一样，随机森林使用的是放回抽样，而这里是不放回抽样。如果取值为1，则全部样本都使用，等于没有使用子采样。如果取值小于1，则只有一部分样本会去做GBDT的决策树拟合。选择小于1的比例可以减少方差，即防止过拟合，但是会增加样本拟合的偏差，因此取值不能太低。推荐在[0.5, 0.8]之间。
#criterion 检测分割质量的函数。有'mse','mae','friedman_mse'。默认的“friedman_mse”通常是最好的，因为在某些情况下可以提供更好的近似。
```

### IsolationForest

孤立森林，用于发现异常值的集成算法。

```python
IsolationForest(n_estimators=100, max_samples='auto', contamination=0.1, max_features=1.0, bootstrap=False, n_jobs=1, random_state=None, verbose=0)
#n_estimators 孤立森林所用树的数目
#max_samples 每个基决策树所需要的样本数。如果输入整数，则为需要分配的样本数，如果为浮点数，则为需要分配的比例(max_samples * X.shape[0])。如果为'auto'则设置为min(256,n_estimators)
#contamination 数据集中离群值的比例
#max_features 每个基学习器的特征数，可输入整数或浮点数，整数为特征数，浮点数为特征比例（max_features * X.shape[1]）。
#bootstrap 如果为True，则单个树使用随机替换的训练数据。 如果为False，则不进行替换的采样。
#n_jobs 算法训练，预测时的并行数，-1表示使用全部核心
#random_state 随机数生成器使用的种子
#verbose 控制建树长度的冗长度。

#孤立森林示例
from sklearn.ensemble import IsolationForest
n=100
If=IsolationForest(n_estimators=n,  contamination=0.0001, n_jobs=24, random_state=None, verbose=0)
If=If.fit(doc2vec1)
py=If.predict(doc2vec2)
precision=sum(test_y[py==1])/sum(py==1)
recall=sum(py[np.array(test_y==1)])/sum(test_y==1)
```

### 不均衡数据处理

在遇到数据比例相差很大的情况时，对于随机森林，可以使用过采样或欠采样，smote等方法使两者比例接近，然后进行建模。而对于gbdf，其本身会通过迭代增加判断小比例样本的系数权重，而如果人为调整数据比例，反而会使模型失去对小比例样本的关注，在实际识别中导致准确率下降。

另外在随机森林树的深度上，深度越大会导致模型整体效果变好，但对小样本的值会更难识别，如果做异常检测，会导致异常值难以发现。

## 文本处理

scikit中自带有对文本处理的函数。

### HashingVectorizer

  `HashingVectorizer`输入为列表格式的文本(支持`'/'`或者`' '`分词的文本)，输出为出现次数的onehot词矩阵。可以使用'l1'或'l2'范数。

```python
#n_features : integer, default=(2 ** 20)输出矩阵中的特征（列）数量。 少量的特征可能会导致散列冲突，但大量数字将导致线性学习者的系数维数更大。
#norm : ‘l1’, ‘l2’ or None, optional范数类型。'l1'或'l2'范数，None为无正则处理
#alternate_sign : boolean, optional, default True
```

### CountVectorizer  

CountVectorizer`输入为列表格式的文本，输出为词出现次数onehot矩阵。

**注意：**当内存报错时可以考虑使用`max_feature`或者`max_df`和`min_df`来进行限制列的数量。也可以考虑使用`dtype`来改变结果类型降低内存损耗。

```python
class sklearn.feature_extraction.text.CountVectorizer(input=’content’, encoding=’utf-8’, decode_error=’strict’, strip_accents=None, lowercase=True, preprocessor=None, tokenizer=None, stop_words=None, token_pattern=’(?u)\b\w\w+\b’, ngram_range=(1, 1), analyzer=’word’, max_df=1.0, min_df=1, max_features=None, vocabulary=None, binary=False, dtype=<class ‘numpy.int64’>)
#input : 输入类型，有三种可选 {‘filename’, ‘file’, ‘content’}
'''
选项‘filename’,表示输入为一个文件名列表
选项‘file’,输入为具有'read'对象的文件 
选项'content'，输入为文本列表
'''
#encoding 输入文件的编码格式
#decode_error : 解码错误时的处理方式，默认为'strict'，{‘strict’, ‘ignore’, ‘replace’}
'''
'strict'当解码错误时会报错
'ignore'当解码错误时会忽视
'''
#analyzer : string, {‘word’, ‘char’, ‘char_wb’} or callable
'''
特征是否应由单词(word)或n元字符(character)组成。 选项'char_wb'仅从字边界内的文本创建字符n-gram; 单词边缘的n-gram用空格填充。
如果通过可调用，它将用于从原始未处理输入中提取特征序列。'''
#stop_words : string {‘english’}, list, or None (default)
'''
如果使用'endlisg'的内置停用词表“英语”。
如果一个list被假定为包含停用词，那么所有这些都将从结果标记中删除。 仅适用于分析器=='单词'。
如果None，则不会使用停用词。 可以将max_df设置为范围[0.7,1.0）中的一个值，以根据术语内部语料库文档频率自动检测和过滤停用词。'''
#lowercase : boolean类型,默认为True。标记之前将所有字符转换为小写。
#token_pattern : string表示什么构成“标记”的正则表达式，仅在analyzer=='word'时使用。 默认的正则表达式选择2个或更多字母数字字符的标记（标点符号被完全忽略并始终被视为标记分隔符）。

#max_df : 保留词语的最大阈值，如果为float，则表示比例，即剔除频数高于对应比例的部分，例如0.8，则剔除频数最高的20%。如果为int，则表示剔除频数高于max_df的部分。
#min_df : 保留词语的最小阈值，如果为float，则表示比例，即剔除频数低于对应比例的部分，例如0.2，则剔除频数最低的20%。如果为int，则表示剔除频数低于max_df的部分。
#max_features : int or None, default=None如果为int，则字典只保留频数最大的max_features个词
#vocabulary : Mapping or iterable, optional
#binary : boolean, default=False 如果为True，则所有非零计数都设置为1.这对建模二元事件而非整数计数的离散概率模型非常有用。
#dtype : type, 由fit_transform（）或transform（）返回的矩阵的类型。
```

### TfidfTransformer

  `TfidfTransformer`输入为`CountVectorizer`的结果，输出为tf-idf，当只有一列时返回为tf即词频

```python
class sklearn.feature_extraction.text.TfidfTransformer(norm=’l2’, use_idf=True, smooth_idf=True, sublinear_tf=False)
#use_idf : boolean, default=True。是否启用文档逆词频
#smooth_idf : boolean, default=True 是否对idf进行平滑，避免零分割 idf(d, t) = log [ (1 + n) / (1 + df(d, t)) ] + 1.
#sublinear_tf : boolean, default=False。应用次线性tf缩放，即用1 + log（tf）替换tf。
```

### TfidfVectorizer

  `TfidfVectorizer`输入为列表格式的文档，输出为tf-idf

```python
class sklearn.feature_extraction.text.TfidfVectorizer(input=’content’, encoding=’utf-8’, decode_error=’strict’, strip_accents=None, lowercase=True, preprocessor=None, tokenizer=None, analyzer=’word’, stop_words=None, token_pattern=’(?u)\b\w\w+\b’, ngram_range=(1, 1), max_df=1.0, min_df=1, max_features=None, vocabulary=None, binary=False, dtype=<class ‘numpy.int64’>, norm=’l2’, use_idf=True, smooth_idf=True, sublinear_tf=False)
#max_df : 保留词语的最大阈值，如果为float，则表示比例，即剔除频数高于对应比例的部分，例如0.8，则剔除频数最高的20%。如果为int，则表示剔除频数高于max_df的部分。
#min_df : 保留词语的最小阈值，如果为float，则表示比例，即剔除频数低于对应比例的部分，例如0.2，则剔除频数最低的20%。如果为int，则表示剔除频数低于max_df的部分。
#max_features : int or None, default=None如果为int，则字典只保留频数最大的max_features个词
```

### 示例

```python
import string
import sys
from sklearn import feature_extraction
from sklearn.feature_extraction.text import TfidfTransformer
from sklearn.feature_extraction.text import CountVectorizer
corpus = []
for line in open('/tpdata/DATA/private/shangguanxf/cc_txt1/create/product1.txt', 'rt').readlines():  #读取一行语料作为一个文档
    corpus.append(line.strip())
vectorizer=CountVectorizer(max_df=0.8, min_df=0.2)#该类会将文本中的词语转换为词频矩阵，矩阵元素a[i][j] 表示j词在i类文本下的词频
transformer=TfidfTransformer()#该类会统计每个词语的tf-idf权值

tfidf=transformer.fit_transform(vectorizer.fit_transform(corpus))#第一个fit_transform是计算tf-idf，第二个fit_transform是将文本转为词频矩阵
word=vectorizer.get_feature_names()#获取词袋模型中的所有词语
weight=tfidf.toarray()#将tf-idf矩阵抽取出来，元素a[i][j]表示j词在i类文本中的tf-idf权重
```

## 特征选择

### Univariate feature selection

通过选择基于单变量统计测试的最佳特征来进行单变量特征选择。 它可以被看作是模型的预处理步骤。 Scikit-learn中进行特征选择的方法有：

- [`SelectKBest`](http://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectKBest.html#sklearn.feature_selection.SelectKBest) 只保留得分最高的k个特征

- [`SelectPercentile`](http://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectPercentile.html#sklearn.feature_selection.SelectPercentile)只保留得分最高的指定百分比的特征

- 对每个特征使用常见的单变量统计检验：假阳性率（false positive rate）用SelectFpr，错误发现率（false discovery rate）用SelectFdr或家族误差（family wise error）用SelectFwe。

- [`GenericUnivariateSelect`](http://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.GenericUnivariateSelect.html#sklearn.feature_selection.GenericUnivariateSelect) 允许使用可配置策略执行单变量特征选择。 这允许使用超参数搜索模型来选择最佳的单变量选择策略。

这些对象将输入一个评分函数，返回单变量分数和p值（分数仅用于SelectKBest和SelectPercentile的特征选择）：

- 对于回归：f_regression，mutual_info_regression

- 对于分类：chi2，f_classif，mutual_info_classif

#### SelectKBest


```python
SelectKBest(score_func=<function f_classif>, k=10)
#score_func 评价单变量特征得分的函数
#k 保留的特征数目

from sklearn.datasets import load_iris
from sklearn.feature_selection import SelectKBest
from sklearn.feature_selection import chi2
iris = load_iris()
X, y = iris.data, iris.target
X.shape

X_new = SelectKBest(chi2, k=2).fit_transform(X, y)
X_new.shape
```

#### SelectPercentile

```python
SelectPercentile(score_func=<function f_classif>, percentile=10)
#score_func 函数采用两个数组X和Y，并返回一对数组（分数，p值）或单个数组与分数。 默认值为f_classif（参见下文“另见”）。 默认功能仅适用于分类任务。
#percentile 保留的特征百分比

from sklearn.datasets import load_iris
from sklearn.feature_selection import SelectKBest
from sklearn.feature_selection import chi2
iris = load_iris()
X, y = iris.data, iris.target
X.shape

X_new = SelectKBest(chi2, k=2).fit_transform(X, y)
X_new.shape
```

#### 评分函数

| 函数名称                   | 描述                 | 适用范围 |
| ---------------------- | ------------------ | ---- |
| chi2                   | 卡方检验               | 分类   |
| f_classif              | 方差分析               | 分类   |
| mutual_info_classif    | 估计离散目标变量的互信息。      | 分类   |
| f_regression           | 单变量线性回归测试(皮尔森相关系数) | 回归   |
| mutual_info_regression | 估计连续目标变量的互信息。      | 回归   |

#### GenericUnivariateSelect

```python
GenericUnivariateSelect(score_func=<function f_classif>, mode='percentile', param=1e-05)
#score_func 函数采用两个数组X和Y，并返回一对数组（分数，p值）或单个数组与分数。 默认值为f_classif（参见下文“另见”）。 默认功能仅适用于分类任务。
#mode 特征选择模式，有{'percentile', 'k_best', 'fpr', 'fdr', 'fwe'}
#param 对应模式的参数，参数类型依赖于模式的选择，可以为浮点或整数型。
```



### SelectFromModel

SelectFromModel是一个基于模型的特征选择器，可以与任何在拟合后具有coef_或feature_importances_属性的模型一起使用。 如果相应的coef_或feature_importances_值低于提供的阈值参数，则这些特征被认为是不重要的并被移除。 除了在数值上指定阈值之外，还有内置的启发式方法用于使用字符串参数来查找阈值。 可用的启发式是'mean'，'mdian'和浮点数，例如“0.1 * mean”。

```python
SelectFromModel(estimator, threshold=None, prefit=False)
#estimator 构建特征选择器的的基准模型。 这可以是一个已经fit过的模型（如果是，则prefit设置为True）或一个没有fit过的模型。
#threshold 用于特征选择的阈值,可以使用字符串{'mean','median'}和浮点数。 保留重要性大于或等于阈值的特征，丢弃其他的特征。 如果'median'（或'mean'），则阈值是特征的median（或mean）。 还可以使用缩放因子（例如，“1.25 * mean”）。 如果None，而且基模型的参数惩罚设置为l1，无论显式还是隐式（例如Lasso），则所使用的阈值为1e-5。 否则，默认使用“mean”。
#prefit 基模型是否进行过预训练。 如果为True，则必须直接调用transform，并且SelectFromModel不能与cross_val_score，GridSearchCV等类似的模型函数一起使用。 如果为False，使用fit训练模型，然后转换以进行特征选择。

from sklearn.ensemble import ExtraTreesClassifier
from sklearn.datasets import load_iris
from sklearn.feature_selection import SelectFromModel
iris = load_iris()
X, y = iris.data, iris.target
X.shape
->(150, 4)
clf = ExtraTreesClassifier()
clf = clf.fit(X, y)
clf.feature_importances_  
->array([ 0.04...,  0.05...,  0.4...,  0.4...])
model = SelectFromModel(clf, '0.2*mean',prefit=True)
X_new = model.transform(X)
X_new.shape
->(150, 2)
```

## 分类度量

### classification_report

```python
sklearn.metrics.classification_report(y_true, y_pred, labels=None, target_names=None, sample_weight=None, digits=2)
#y_true : 真实值。1d array-like, or label indicator array / sparse matrix 
#y_pred : 预测值。1d array-like, or label indicator array / sparse matrix
#labels : 输出时标签的排列顺序。array, shape = [n_labels]
#target_names : 输出时标签的展示名字，顺序和labels中相同。list of strings
#sample_weight : 样本权重。array-like of shape = [n_samples], optional
#digits : 格式化输出浮点值的位数。int


from sklearn.metrics import classification_report
y_true = [0, 1, 2, 2, 2]
y_pred = [0, 0, 2, 2, 1]
target_names = ['class 0', 'class 1', 'class 2']
print(classification_report(y_true, y_pred, target_names=target_names))
```

## 模型验证

### 划分训练集数据集

```python
sklearn.model_selection.train_test_split(*arrays, **options)
#arrays 输入数据，可以为list，np.array,scipy-sparse matrices or pandas dataframes
#test_size : 训练集尺寸，如果输入为float，需在0到1之间，如果为Int则为抽样数目，如果为None，则依赖于train_size，如果train_size也未指定，则默认为0.25,
#train_size :  训练集尺寸，如果输入为float，需在0到1之间，如果为Int则为抽样数目，如果为None，则依赖于test_size
#random_state :如果是int，random_state是随机数发生器使用的种子; 如果RandomState实例，random_state是随机数生成器; 如果为None，则随机数生成器是np.random使用的RandomState实例。
#shuffle : 分裂前是否洗牌数据。 如果shuffle = False，那么stratify必须是None。
#stratify : 如果不是None，则数据以分层方式分割，将其用作类标签。

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)

```

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

## 模型保存

注意当保存的模型需要在py2和py3中使用时，需要注意保存时的protocol参数。即下面两种方法中的dump参数

Pickle uses different `protocols` to convert your data to a binary stream.

- In python 2 there are [3 different protocols](https://docs.python.org/2/library/pickle.html#data-stream-format) (`0`, `1`, `2`) and the default is `0`.
- In python 3 there are [5 different protocols](https://docs.python.org/3/library/pickle.html#data-stream-format) (`0`, `1`, `2`, `3`, `4`) and the default is `3`

```python
#方法一
from sklearn.externals import joblib
#lr是一个LogisticRegression模型
#保存模型
joblib.dump(lr, 'lr.model')
#加载模型
lr = joblib.load('lr.model')

#方法二
from sklearn import svm
from sklearn import datasets
s=pickle.dumps(clf)
f=open('svm.txt','w')
f.write(s)
f.close()
f2=open('svm.txt','r')
s2=f2.read()
clf2=pickle.loads(s2)
clf2.predict(X[0:1])

```

