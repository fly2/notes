[TOC]
# pandas使用记录#

## 小知识

### pandas对于read_csv比read_excel要快。

### xlsx格式可以保存DataFrame的时间格式，csv无法保存。时间格式导出到csv再回去时会变成str格式。

### 批量处理文件夹里的所有文件

```python
import pandas as pd
import os
#地图数据转换
for root,dirs,files in os.walk('/home/weblogic/DATA/public/cc热点大数据/工单机构分布/工单机构分布（综合）/'):
    for filespath in files:
        #root为路径，files为文件列表
        print(os.path.join(root,filespath))
        df=pd.read_csv(os.path.join(root,filespath),encoding="gb18030")
        #用os.path.join合成路径
        df1.to_csv(os.path.join('/home/weblogic/DATA/private/shangguanxf/cc_bigdata/create/工单机构分布/标准化',filespath),encoding='gb18030')
```

### 使用sample对数据进行重采样

```python
DataFrame.sample(n=None, frac=None, replace=False, weights=None, random_state=None, axis=None)
#n 返回的采样数据
#frac 返回的采样比例
#replace 采样是否替换原有数据
#weights 采样权重，需与原df列同样长。缺失值被视为0。

#通过设置采样比例为1，对数据进行重新排序。
df=df.sample(frac=1)
df.reset_index(inplace=True, drop=True)
```



## DataFrame常用操作

### 得到列名

```python
df.column[0]
#得到第0列的列名
```

### 更改列名

   ```python
   import pandas as pd
   df=pd.DataFrame({'A':[1,2,3], 'B':[4,5,6], 'C':[7,8,9]})

   #方法一，需要一次更改全部列名
   df.columns('a','b','c')
   #方法二,更改指定列名
   df.rename(columns={'a':'A'},inplace=True)
   ```

### 插入列

```python
#直接在最后添加列
df=pd.DataFrame()
df['列']=a

#在指定位置插入列
DataFrame.insert(loc, column, value, allow_duplicates=False)
#loc插入的位置，在此列及之后的列向后顺延
#column插入列的列名
#value插入的值
#allow_duplicates=False即不允许插入列与已有列名重复
df.insert(9,'班次',d)
```

### 删除列

```python
#永久删除列
del df['列']

#返回一个删除列的新的DataFrame
DataFrame.drop(labels, axis=0, level=None, inplace=False, errors='raise')
#labels标签名或标签名的列表
#axis轴，当label为1时，axis=1
#implace是否代替原Dataframe，默认为False

df.drop([df.columns[0]], axis=1,inplace=True)
#df.column[0]得到第0列的列名
```

### 按照条件更改列的值

```python
#使用.loc来按照条件更改列的值
df.loc[df['A'] > 2, 'B'] = new_val
```



### 数据合并

数据合并有两种，一种是行数不变，合并列。一种是列不变，合并行

```python
#列不变，增加行（合并行）
df2=pd.concat([df,df1])
#df和df1的列名及排列要一致

#行不变，增加列（合并列）
df4=pd.merge(df3,df1,how='inner',on=None,left_on=['投保人姓名','保单号'],right_on=['投保人姓名','保单号']
#根据多列去做关联，需要两列都相同才关联
#how有'left','right','inner'三种，用法和sql中一致
#on为关联时匹配的列，当只有一列时用on，多列时on=None，用left_on和right_on来关联
main=pd.merge(infor4,infor2,how='left',on='姓名')
#用名字去关联，左边保持不变，右边和左边相同时匹配过去
```

### dict转DataFrame

dict转DataFrame的三种方法。转成列表再导入速度最快。

```python
#用for循环主键，依次导入。用时：45.9755654335022   
df1=pd.DataFrame(columns=('word','value'))
n=0
for i in one_hot.keys():
   df1.loc[n]=[i,one_hot[i]]
   n=n+1
    
#将dict转成列表，导入list。用时：0.010346174240112305
df1=pd.DataFrame(list(one_hot.items()), columns=['Date', 'DateValue'])

#将dict直接导入，再重命名列名。用时：0.024332761764526367
df1=pd.DataFrame({'value':one_hot})
df1.index.name='key'
df1=df1.reset_index()
```

### df中str函数使用

python str本身自带了很多函数。在pandas中使用需要在str类型的series后加str。**不加.str将无法有效进行替换。**

 ```python
   infor['日期']=infor['日期'].str.replace('-','')#字符替换
    
   df1.通话文本.str.count('。')#字符计数
   
   df1.通话文本.str.len()#得到每一行的字符长度
 ```

### 连接数据库

使用python连接数据库，可以使用sqlalchemy包，通过这个包连接数据库，对里面的数据进行操作。也可以通过pandas内部的函数直接读取数据库中的表。针对oracle，可以使用cx_Oracle包来读取oracle数据。读取oracle数据库出现中文乱码，是由于编码不匹配，需在前面设置编码格式。

```python
#设置编码格式
import os
os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.UTF8'
#用sqlalchemy创立连接
from sqlalchemy import create_engine
engine = create_engine('oracle+cx_oracle://shangguan:shangguan_test123@sandbox')

#设置编码格式
import os
os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.UTF8'
#直接读取数据库中的表
df=pd.read_sql_table('id_infor','oracle+cx_oracle://shangguan:shangguan_test123@sandbox')

#设置编码格式
import os
os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.UTF8'
import cx_Oracle as ora
import pandas as pd
con = ora.connect('shangguan/shangguan_test123@sandbox')
cur = con.cursor()
cur.execute('select * from cc_text_question')
res = cur.fetchall()[][].read()
data = eval(res)
df = pd.DataFrame(res, columns = ['保单号', '录音编号', '问答文本'])
print(df)
```
### str类型的series转int类型

将str的series转换为int的方法。

```python
#使用pandas自带函数
df1.录音编号=pd.to_numeric(df1.录音编号)
#使用numpy类型转换
df1.录音编号=np.int64(df1.录音编号)
```
## 时间类型处理

参考链接:

[pandas 时间序列操作](http://zqdevres.qiniucdn.com/data/20150813205120/index.html)

[pandas时间和日期处理](http://www.360doc.com/content/16/0902/15/1317564_587787225.shtml)

导入的包

```python
from datetime import time
from pandas.tseries.offsets import *
from datetime import datetime,timedelta
```

### str转换为时间格式

```python
pd.to_datetime(df.工单生成时间,format='%Y/%m/%d')
#df.工单生成时间是格式为'2016/08/12'类型的str的列
#format识别的字符串格式
```

### 标准时间格式转换其他时间格式

```python
df.工单生成时间.dt.strftime('%Y-%m-%d')
#将时间转换为年月日的格式
```

### 进行时间计算

```python
DateOffset(months=3,days=10)
#时间格式的加减要通过DateOffset转换才能运算。

#得到当前时间
datetime.now()==>datetime.datetime(2017, 4, 15, 16, 2, 24, 663820)
#加3月10天
datetime.now()+DateOffset(months=3,days=10)==>Timestamp('2017-07-25 16:03:17.801300')

```

### 转换到月初

```python
MonthBegin()
#将时间转换到月初

#减MonthBegin()转换到本月月初
datetime.now()+DateOffset(months=3,days=10)-MonthBegin()==>Timestamp('2017-07-01 16:05:47.921094')
#加MonthBegin()转换到下月月初
datetime.now()+DateOffset(months=3,days=10)+MonthBegin()==>Timestamp('2017-08-01 16:09:18.443141')
```

### 生成时间范围的列表

```python
pandas.date_range(start=None, end=None, periods=None, freq='D', tz=None, normalize=False, name=None, closed=None, **kwargs)[source]
#start：string或datetime-like，默认值是None，表示日期的起点。
#end：string或datetime-like，默认值是None，表示日期的终点。
#periods：integer或None，默认值是None，表示你要从这个函数产生多少个日期索引值；如果是None的话，那么start和end必须不能为None。
#freq：string或DateOffset，默认值是’D’，表示以自然日为单位，这个参数用来指定计时单位，比如’5H’表示每隔5个小时计算一次。
#tz：string或None，表示时区，例如：’Asia/Hong_Kong’。
#normalize：bool，默认值为False，如果为True的话，那么在产生时间索引值之前会先把start和end都转化为当日的午夜0点。
#name：str，默认值为None，给返回的时间索引指定一个名字。
#closed：string或者None，默认值为None，表示start和end这个区间端点是否包含在区间内，可以有三个值，’left’表示左闭右开区间，’right’表示左开右闭区间，None表示两边都是闭区间。

pd.date_range('2011-01-02','2016-11-30',freq='W')
#按周生成时间。起始时间为'2011-01-02'，截止时间为'2016-11-30'
```

## 问题解决记录

### pandas读取csv，前面有\ufeff

用pandas读取csv时，有时前面会有\ufeff，这是因为读取为'utf-8'+bom文件。可以通过'utf-8-sig'读取方式解决。

```python
#用utf-8-sig格式读取
df1=pd.read_csv('/home/weblogic/DATA/private/shangguanxf/cc_txt2/疑似误导录音/疑似诱导录音文本.csv',encoding='utf-8-sig',header=None,names=['录音编号','通话文本'])
```
### 根据特定列去除包含空值的行

df.dropna(axis=0)为去除包含空值的行，无法指定只根据特定行删除。如果不需要和其他值关联，可以使用`df.坐席原始意见..dropna(axis=0)`来获取特定列去除空值后的结果。

```python
df4=df4[df4.坐席原始意见.isnull()==False]
#1.df4.坐席原始意见.isnull() 得到这一列是否为空的真值列（空为真，非空为假）
#2.通过==False将非空列转化为真
#3.df4[df4.坐席原始意见.isnull()==False]获取df4中为空的行

df.isnull()
#将返回一个真值矩阵，空为真，非空为假
```

### 读取的csv没有列名

```python
df=pd.read_csv(os.path.join(root,filespath),encoding="gb18030",header=None,names=['机构名称','工单客户比'])
#注意，设置列名时需header=None
```

### 生成训练集和测试集

```python
#取70%为训练集，得到再采样的70%的index
train_list=list(all_.sample(frac=0.7).index)
#通过set得到全量的index和训练集的index的差集作为测试集
test_list=list(set(list(range(len(all_))))-set(train_list))
#通过index取得训练集
train=all_.iloc[train_list,:]
train.reset_index(inplace=True, drop=True)
#训练集中目标分类数量（分类为1）
print(sum(train.标签))
#通过index取得测试集
test=all_.iloc[test_list,:]
test.reset_index(inplace=True, drop=True)
#测试集中目标分类数量（分类为1）
print(sum(test.标签))
```



