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
        #没有找到.csv会返回-1，否则返回位置
        if filespath.find('.csv')>-1:
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

### iloc和loc

pandas中iloc是用来对位置索引进行操作，loc是用行索引进行操作。

## DataFrame常用操作

###建表

```python
#先建立表，再逐步赋值
df1=pd.DataFrame(columns=('机构名称','minmax变换乘100','工单/客户数','该机构工单量','有效客户数'))
#用loc为表赋值
df1.loc[n]=[df.机构名称[i],m3,df.工单机构分布[i],df.该机构工单量[i],df.有效客户数[i]]

#通过直接赋值的方法建立表
df2 = pd.DataFrame({ 'A' : 1,
                     'B' : pd.Timestamp('20130102'),
                     'C' : pd.Series(1,index=list(range(4)),dtype='float32'),
                     'D' : np.array([3] * 4,dtype='int32'),
                     'E' : pd.Categorical(["test","train","test","train"]),
                     'F' : 'foo' })
#生成离散值列
df3 = pd.DataFrame({ 'A' : 1,
                     'G' : pd.np.random.choice(['lol','dota','cf'],10)})

#通过列直接建表
#此法只可建一列，第二列为index，当有多列值时和上面一样，需要用字典去赋值。
a=[1,2,3]
b=['n','n','m']
df4=pd.DataFrame(a,b)
#pandas.DataFrame(data=None, index=None, columns=None, dtype=None, copy=False),故df4中a为列，b为索引。如果想a,b均为列，需要加{}，如df2那般
```

### 数据选择

通过标签（df.loc）选择时行[1:3]表示[1,2,3],通过位置(df.iloc)进行选择时行[1:3]表示[1,2]

```python
import pandas as pd
import numpy as np
df=pd.DataFrame({ 'A' : 1,
                     'B' : pd.Timestamp('20130102'),
                     'C' : pd.Series(1,index=list(range(4)),dtype='float32'),
                     'D' : np.array([3] * 4,dtype='int32'),
                     'E' : pd.Categorical(["test","train","test","train"]),
                     'F' : 'foo' })
#按列选择,选择多列时需用括号
df[['A','B']]

#按行选择,0:3表示[0,1,2]
df[0:3]

#通过布尔值选择
df[df.E=='test']
#用isin进行筛选
df[df.E.isin(['test','train'])]

#通过标签获得行列,0:3为行标签即index，['A','B']为列名
注意：此时0:3得到的是[0,1,2,3]
df.loc[0:3,['A','B']]
df.loc[0]<==>df.loc[0,:]#两者结果相同
#loc行可以为bool值
df.loc[df.E=='test',['A','B']]


#通过位置获得行列,0:4为行位置，0:2为列位置
注意：行列都是从0开始计数
注意：此时0:4=[0,1,2,3],0:2=[0,1]
df.iloc[0:4,0:2]
#单独取一列时可直接使用该列位置.直接取C列的值
df.iloc[0:4,2]

```

### 数据赋值

建议使用loc和iloc进行赋值

```python
#通过索引进行数据赋值
df.loc[0,['A','B']=[2,3]
#at和loc等价
df.at[0,['A','B']=[2,3]
      
#通过位置进行数据赋值
df.iloc[0:3,2]=3
#iat和iloc类似，但行列只能单选
df.iat[0:3,2]=3
```

### 行索引及多重行索引

```python
import pandas as pd
#单列索引
df=pd.DataFrame(columns=['age','gender','name'])
#利用索引新添数据，1,2,3为当前行索引
df.loc[1]=[18,'female','July']
df.loc[2]=[19,'male','Jeo']
df.loc[3]=[12,'female','Lily']
df.loc[4]=[18,'male','Jack']
#设置索引
df.set_index('age',inplace=True)
#选择数据
df.loc[19]
>>
gender    male
name       Jeo
Name: 19.0, dtype: object

#多列索引
df.set_index(['age','gender'],inplace=True)
#选择数据
df.loc[(18,'female')]
>>
name    July
Name: (18.0, female), dtype: object
#利用查询来筛选数据
df.query('age==18')
>>
		        name
age	    gender	
18.0	female	July
        male	Jack
```

### 重置索引

在使用pd.concat之后需要重置索引，否则df.iloc[0,1]返回合并前每个表的[0,1]

```python
#重置索引
df.reset_index(inplace=True, drop=True)
#inplece=True 代替原df
#drop=True 删除原索引

#重新排列索引
df.reindex(new_index)
#将原来的索引按照新索引进行排列，如果新索引在原索引中不存在，返回NaN。如果想要生成新的索引，使用reset_index()
```

### 索引排序

```python
df.sort_index(axis=0, level=None, ascending=True, inplace=False, kind='quicksort', na_position='last', sort_remaining=True, by=None)
#axis=0 按列排序
#level 排序等级，可以为索引等级（int）或索引名称（level name）或者为list格式
#ascending 是否正序排列
#inplace 是否替代原数据
#kind 排列方式{‘quicksort’, ‘mergesort’, ‘heapsort’}
#na_position {'first', 'last'}Nan的数据摆放位置，first为放在数据前面，last为放在末尾
#sort_remaining 如果为真，并按级别和索引排序为多级，则按照指定级别排序后，按其他级别排序（按顺序排列）
```

### 索引操作

```python
#取交集
df.index.intersection(df1.index)
#取并集
df.index.union(df1.index)
#取差集
df.index.diff(df1.index)
```

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
   df.columns=['a','b','c']
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

### 删除行列

```python
#返回一个删除行/列的新的DataFrame
DataFrame.drop(labels, axis=0, level=None, inplace=False, errors='raise')
#labels标签名或标签名的列表
#axis轴，当label为列名时，axis=1
#implace是否代替原Dataframe，默认为False

#删除列

del df['列名']

df.drop([df.columns[0]], axis=1,inplace=True)
#df.column[0]得到第0列的列名

#删除行
df.drop(0)
#删除标签为0的行

df.drop(0,inplace=True)
#删除行标签为0的行，并代替原表
```

### 排序列

```python
pandas.DataFrame.sort_values(by, axis=0, ascending=True, inplace=False, kind='quicksort', na_position='last')
#by 排序所依赖的轴的名称或名称列表
#axis=0 按什么轴进行排序，0为行，1为列。默认为0
#ascending 是否升序，应为bool或bool列表。默认为True升序
#inplace 是否替换原表。默认为否
#kind 排序方法，有{‘quicksort’, ‘mergesort’, ‘heapsort’}，默认为'quicksort'
#na_position NaN的位置,有{‘first’, ‘last’}，放在开始还是结尾，默认放结尾

import numpy as np
import pandas as pd
df = pd.DataFrame({'A' : ['foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'foo'],
'B' : ['one', 'one', 'two', 'three','two', 'two', 'one', 'three'],
'C' : np.random.randn(8),'D' : np.random.randn(8)})
#按照C列排序，递增为关
df.sort_values('C',ascending=False)
#按照b，c列排序
df.sort_values(['B','C'])
```

### 更改列类型

更改类型需要使用astype()函数，有三种使用方法。astype()里可以用numpy的数据类型，也可以直接用'int8'类型的字符串形式。

**注意：**用第二种方法更改部分列时，即使只有一列也需要用`dft[['a']]`，因为`dft.a`和`dft['a']`都得到的是array形式，`dft[['a']]`得到的是df格式。只有df格式才能用astype。

```python

dft = pd.DataFrame({'a': [1,2,3], 'b': [4,5,6], 'c': [7, 8, 9]})
#全表更改类型
#不会直接更改原表，需要赋值回去
dft=dft.astype(np.int16)
dft=dft.astype('int16')

#更改部分列
#不会直接更改原表，需要赋值回去
dft[['a','b']] = dft[['a','b']].astype(np.uint8)

#用loc更改部分列，容易出现异常，导致列格式无法传递回去,要避免使用第三种方法
dft.loc[:, ['a', 'b']].astype(np.int16).dtypes
>
a    int16
b    int16
dtype: object
    
dft.loc[:, ['a', 'b']] = dft.loc[:, ['a', 'b']].astype(np.int16)
dft.dtypes
>    
a    int64
b    int64
c    int64
dtype: object
```

### 得到重复值

```python
df.duplicated(cols=None, take_last=False)
#cols 为考虑是否重复的列，None则考虑全表
#take_last为重复值标签方式{'first','last',False}
#first:重复值中第一个为false，即不视为重复值
#last：重复值中最后一个为false，即不视为重复值
#False：所有重复值均为True。
```

### 去除重复值

```python
infor2.drop_duplicates(subset,keep,inplace)
#subset 为所需的列标签或行标签，可以一个，也可以是标签的列表
#keep的值有三种{‘first’, ‘last’, False},默认为‘first’
#first : 保留重复值中的第一个
#last : 保留重复值中的最后一个
#False : 删除所有重复值
#inplace=True代替原df，默认为False
infor2.drop_duplicates(['工作所在地','性别'],False,inplace=True)
#删除所有性别，工作所在地重复行
```

### 得到nan

pandas 提供isnull和notnull两种方法。isnull是为nan返回True，notnull是nan返回False。

```python
df1=pd.DataFrame({'A':range(5),'B':[np.nan,np.nan,np.nan,3,1],'C':np.random.rand(5)})
#使用pd函数
pd.isnull(df1.B)
pd.notnull(df1.B)

#使用df的函数
df1.B.isnull
df1.B.notnull
```

### 去除nan

```python
dropna(axis=0, how='any', thresh=None, subset=None, inplace=False)
#axis 按行还是按列去除，axis=0，按行删，axis=1按列删
#how{'any','all'} 删除条件，'any'删除所有存在空值的行/列，'all'当该列或行全为空时删除
#thresh 不被去除需要的最少非空值数
#subset 只根据subset参数里的列进行操作
#inplece 是否代替原数据

df1=pd.DataFrame({'A':range(5),'B':[np.nan,np.nan,np.nan,3,1],'C':np.random.rand(5),'D':[1,2,3,4,np.nan]})
df1
	A	B	       C	D
0	0	NaN	0.494810	1.0
1	1	NaN	0.001969	2.0
2	2	NaN	0.774776	3.0
3	3	3.0	0.073526	4.0
4	4	1.0	0.532342	NaN
df1.dropna(axis=0,subset=['D'])#只根据列表中的列的空值来决定是否保留，保留方式按how的参数执行
    A	 B	       C	D
0	0	NaN	0.494810	1.0
1	1	NaN	0.001969	2.0
2	2	NaN	0.774776	3.0
3	3	3.0	0.073526	4.0
df1.dropna(axis=0,thresh=4)#只保留非空值大于等于4的行
    A	B	       C	D
3	3	3.0	0.073526	4.0
```

### 统计各列的值的数目

```python
#利用series.value_counts()计算列中每个值的数目
Series.value_counts(normalize=False, sort=True, ascending=False, bins=None, dropna=True)
#normalize 是否转换成频率，normalize=True则频数除以总频数作归一化处理
#sort 是否排序
#ascending 是否升序 默认ascending=False降序排列
#bins=int 将连续变量分成离散变量
#dropna=True删除空值

#对test列统计值的词数
a=['一','二','一']
df=pd.DataFrame({'test':a})
df.test.value_counts()
#用bins将其分组
a=[1,2,3,4,5,6,1,2,3]
df=pd.DataFrame({'test':a})
df.test.value_counts(bins=2)
#结果
0.995    6
3.500    3
#即将列a分成两个离散变量，(0.995,3.5] 频数为6, (3.5,6]频数为3
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

   df1.通话文本.str.find('中')#得到第一个‘中’（查询的字符串）出现的位置
 ```

### str类型的series转int类型

将str的series转换为int的方法。

```python
#使用pandas自带函数
df1.录音编号=pd.to_numeric(df1.录音编号)
#使用numpy类型转换
df1.录音编号=np.int64(df1.录音编号)
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
#通过schema参数设置表所在空间（即name.table的name）
#df=pd.read_sql_table('tb_cc_record_analysis','oracle+cx_oracle://shangguan:shangguan_test123@sandbox',schema='sandbox_fixed')

#设置编码格式
import os
os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.UTF8'
#用cx_Oracle读取数据库中的表
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
## 文件读存

### csv

```python
#csv读取
pd.read_csv(filepath_or_buffer, sep=', ', delimiter=None, header='infer', names=None, index_col=None, usecols=None, squeeze=False, prefix=None, mangle_dupe_cols=True, dtype=None, engine=None, converters=None, true_values=None, false_values=None, skipinitialspace=False, skiprows=None, nrows=None, na_values=None, keep_default_na=True, na_filter=True, verbose=False, skip_blank_lines=True, parse_dates=False, infer_datetime_format=False, keep_date_col=False, date_parser=None, dayfirst=False, iterator=False, chunksize=None, compression='infer', thousands=None, decimal=b'.', lineterminator=None, quotechar='”', quoting=0, escapechar=None, comment=None, encoding=None, dialect=None, tupleize_cols=False, error_bad_lines=True, warn_bad_lines=True, skipfooter=0, skip_footer=0, doublequote=True, delim_whitespace=False, as_recarray=False, compact_ints=False, use_unsigned=False, low_memory=True, buffer_lines=None, memory_map=False, float_precision=None)
只列出常用参数，其余用时查询。
#filepath_or_buffer 文件地址
#sep设置分隔符
#engine : {‘c’, ‘python’}, 选用引擎，c速度快，python功能更丰富。 
#encoding 编码格式，默认为utf-8


#常用写法
df=pd.read_csv('/home/weblogic/DATA/public/cc热点大数据/太寿工单/太寿工单.csv',encoding='gb18030')

#csv写出
df.to_csv(path_or_buf=None, sep=', ', na_rep='', float_format=None, columns=None, header=True, index=True, index_label=None, mode='w', encoding=None, compression=None, quoting=None, quotechar='”', line_terminator='\n', chunksize=None, tupleize_cols=False, date_format=None, doublequote=True, escapechar=None, decimal='.')
只列出常用参数，其余用时查询
#path_or_buf 保存文件位置
#sep 设置分割符
#header 是否保留列名，默认header=True保留列名
#index 是否保留索引，默认index=True保留索引
#encoding 编码格式

#常用写法.因为read_csv读取时会自动生成索引，所以生成时关闭索引
df.to_csv('/home/weblogic/DATA/private/shangguanxf/cc_copy/create/10-3/寿险投诉机构工单.csv',index=False)
```

#### 读取的csv没有列名

```python
df=pd.read_csv(os.path.join(root,filespath),encoding="gb18030",header=None,names=['机构名称','工单客户比'])
#注意，设置列名时需header=None
```

#### 读取csv有不规范数据，有的行中列和其他行不统一

通过设置 error_bad_lines=True, warn_bad_lines=True来决定是否遇到不合规范的行数进行报错和警告。

**注意：**如果error_bad_lines=False,虽然不会报错，但会跳过不合规范的数据

```python
CParserError: Error tokenizing data. C error: Expected 10 fields in line 45, saw 11
报错：数据期待为10列，但是在45行见到了11列

df=pd.read_csv('/home/weblogic/DATA/private/shangguanxf/cc_txt1/warning.txt',encoding='gb18030',error_bad_lines=False)
#遇到数据格式错误会不进行报错，但仍会跳出警告
#报错的数据会被跳过
```

### excel

```python
#读取excel
pd.read_excel(io, sheetname=0, header=0, skiprows=None, skip_footer=0, index_col=None, names=None, parse_cols=None, parse_dates=False, date_parser=None, na_values=None, thousands=None, convert_float=True, has_index_names=None, converters=None, dtype=None, true_values=None, false_values=None, engine=None, squeeze=False, **kwds)
只列出常用参数，余下用时查询
#io 文件位置
#sheetname 选择所用的sheet，可用string或int。string则应为所取sheet名。int则是sheet位置，0为第一个sheet。当取多个sheet时，使用列表[]。如果为None，则取所有的sheet
#header 列名位置，默认为0
#names 要使用的列名列表。 如果文件不包含标题行，应设置header = None

#常用写法
df1=pd.read_excel('/home/weblogic/DATA/private/shangguanxf/cc_copy/1-3/寿险预约与咨询工单1_3.xls','Sheet1')

#保存excel
df.to_excel(excel_writer, sheet_name='Sheet1', na_rep='', float_format=None, columns=None, header=True, index=True, index_label=None, startrow=0, startcol=0, engine=None, merge_cells=True, encoding=None, inf_rep='inf', verbose=True, freeze_panes=None)
#excel_writer 保存文件位置
#sheet_name 表格名称
#encoding 编码格式

#常用写法
df.to_excel(os.path.join('/home/weblogic/DATA/private/shangguanxf/cc_copy/create/',res_path,filespath1),sheet_name='sheet1')
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

## 分组计算

### groupby分组

```python
import numpy as np
import pandas as pd
df = pd.DataFrame({'A' : ['foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'foo'],
'B' : ['one', 'one', 'two', 'three','two', 'two', 'one', 'three'],
'C' : np.random.randn(8),'D' : np.random.randn(8)})
#按A列进行分组
df1=df.groupby('A')
#按A、B列进行分组
df2=df.groupby(['A','B'])
#按索引进行分组
df3=df.groupby(level=0)
```

### apply使用

apply对整行或整列的进行操作，类似matlab中cellfun对单个cell进行操作。

```python
DataFrame.apply(func, axis=0, broadcast=False, raw=False, reduce=None, args=(), **kwds)
#func 对行/列进行操作的函数，可以自定义函数，多使用匿名函数
#axis=0 0为行，1为列即操作一列的所有行
#broadcast=False 是否和输入dataframe的尺寸保持一致
#raw=False 是否转换为Series格式，False为转换为Series格式，True则使用ndarray代替。如果只使用apply numpy函数，使用ndarray将得到更高的性能。
#reduce
#args 传递给函数的位置参数
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

### 文本特征中错误分行

数据中的文本在转换过程中空格被转换成换行符。

```python
#将语音文本数据中的错误分开的文本合并
pathname='/home/weblogic/DATA/private/shangguanxf/cc_txt2/大数据语音文本信息.txt'
#二进制读取文件
f = open(pathname, 'rb')  
line=f.readlines()
for i in range(2,len(line)):
    #解码文本
    s=line[i].decode()
    #只保留数字行前面的换行符。正常一行应有6列，由于空格被转换成换行符，导致最后一列文本被错分多行。删除非6列的行的换行符，合并文本数据。
    if s.count(',')<6:
        line[i-1]=line[i-1][:-2]
with open ('/home/weblogic/DATA/private/shangguanxf/cc_txt2/大数据语音文本信息.csv','wb') as file2:
    file2.writelines(line)    
print('end')
```

### DataFrame相加值全为nan

dataframe相加需要列名一致，否则会导致结果全为nan。将一个dataframe的列名与另一个dataframe改一致。

```python
df1=pd.DataFrame({'A':[1,2,3], 'B':[4,5,6], 'C':[7,8,9]})
df2=pd.DataFrame({'a':[1,2,3], 'b':[4,5,6], 'c':[7,8,9]})
df1+df2
-->A	B	C	a	b	c
0	NaN	NaN	NaN	NaN	NaN	NaN
1	NaN	NaN	NaN	NaN	NaN	NaN
2	NaN	NaN	NaN	NaN	NaN	NaN
#更改列名
df1.columns=df2.columns
df1+df2
-->a	b	c
0	2	8	14
1	4	10	16
2	6	12	18
```

### 查看df中索引是否存在

```python
import pandas as pd
df1=pd.DataFrame({'A':[1,2,3], 'B':[4,5,6], 'C':[7,8,9]})
#isin(list),list为需要确认是否存在的索引
df.index.isin([3])
->array([False, False,  True, False], dtype=bool)
#使用逻辑词直接判断
3 in df.index
->True
#可以结合if进行使用
if 3 in df.index:print('ok')
```

### np数组无法用df真值赋值

将df真值转为np真值即可成功使用

```python
a=np.array([1,2,3,4,5,6,7,8])
df=pd.DataFrame({'A':[1,2,3,4,5,6,7,8]})
#直接用df的真值去操作
a[df.A>3]
-->array([1, 1, 1, 2, 2, 2, 2, 2])
a[df.A>3]=1
a
-->array([1, 1, 3, 4, 5, 6, 7, 8])
#将df的真值转为np数组
a[np.array(df.A>3)]
-->array([4, 5, 6, 7, 8])
a[np.array(df.A>3)]=1
a
-->array([1, 2, 3, 1, 1, 1, 1, 1])
```

### 判断df是否存在

```python
df is None
#为空返回真
df is not None
#不为空返回真
```



