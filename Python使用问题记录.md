## Python使用问题记录

### 条件运算

在条件运算时，&和|比>,<运算等级高，要注意加括号以保证结果正确。

### np的bool索引赋值

在对np生成的数组以bool索引赋值时，bool需要为np.array类型。

```python
true=alltime2.loc[:,tlist1].sum(axis=1)>0
buy=np.zeros(len(alltime2),'int8')
buy[np.array(true)]=1
```

