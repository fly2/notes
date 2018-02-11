# sicikit-learn使用记录

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

### text

  scikit中自带有对文本处理的函数。

  `HashingVectorizer`输入为列表格式的文本(支持'/'分词的文本)，输出为onehot词矩阵，出现为1，未出现为0
  `CountVectorizer`输入为列表格式的文本，输出为词出现次数onehot矩阵

  `sklearn.feature_extraction.text.``CountVectorizer`(*input=u'content'*, *encoding=u'utf-8'*, *decode_error=u'strict'*, *strip_accents=None*, *lowercase=True*, *preprocessor=None*, *tokenizer=None*, *stop_words=None*, *token_pattern=u'(?u)\b\w\w+\b'*, *ngram_range=(1*, *1)*, *analyzer=u'word'*, *max_df=1.0*, *min_df=1*, *max_features=None*, *vocabulary=None*, *binary=False*, *dtype=<type 'numpy.int64'>*)

  `TfidfTransformer`输入为`CountVectorizer`的结果，输出为tf-idf，当只有一列时返回为tf即词频

  `TfidfVectorizer`输入为列表格式的文档，输出为tf-idf

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

## 模型保存

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

