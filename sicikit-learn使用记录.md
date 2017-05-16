## sicikit-learn使用记录

### Nearest Neighbors 

  最近邻有两种方法，一种是根据最近的邻居表现做分类（回归），一种是根据一定范围半径r内的邻居的表现做分类(回归)。  
  在用KNeighborsRegressor或RadiusNeighborsRegressor做回归，即对近邻的值做平均`weights = 'uniform'`,也可以按照距离加权平均`weights = 'distance'`。**在scikit-learn中distance加权是按照距离倒数作加权**

### text

  scikit中自带有对文本处理的函数。

  `HashingVectorizer`输入为列表格式的文本，输出为onehot词矩阵，出现为1，未出现为0
  `CountVectorizer`输入为列表格式的文本，输出为词出现次数onehot矩阵
  `TfidfTransformer`输入为`CountVectorizer`的结果，输出为tf-idf，当只有一列时返回为tf即词频

  `TfidfVectorizer`输入为列表格式的文档，输出为tf-idf

### gbdt

  GradientBoostingClassifier和GradientBoostingRegressor进行训练时，x需要为array-like格式(数组)，不能使用稀疏矩阵。当为稀疏矩阵时，可以考虑RandomForest或adaboost。

### cross-validation

  可以通过cross_val_score和cross_val_predict进行交叉验证。

```python
sklearn.model_selection.cross_val_score(estimator, X, y=None, groups=None, scoring=None, cv=None, n_jobs=1, verbose=0, fit_params=None, pre_dispatch='2*n_jobs')
#estimator用来训练数据的模型
#X输入的变量。不可使用稀疏矩阵
#y目标变量
#groups分类标签，将数据集分成测试集和训练集的分类标签，使样本抽样平衡。
#scoring
```

### 模型保存方法

```python
from sklearn.externals import joblib
joblib.dump(clf, 'filename.pkl') 
clf = joblib.load('filename.pkl') 
```

