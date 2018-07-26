## pyspark使用记录

### 读取数据到df

#### json

```python

from pyspark.sql import SparkSession
park = SparkSession \
        .builder \
        .appName("Python Spark SQL basic example") \
        .config("spark.some.config.option", "some-value") \
        .getOrCreate()
df = spark.read.json("file:///usr/local/spark/examples/src/main/resources/people.json")
```

#### csv

```python
csv(path, schema=None, sep=None, encoding=None, quote=None, escape=None, comment=None, header=None, inferSchema=None, ignoreLeadingWhiteSpace=None, ignoreTrailingWhiteSpace=None, nullValue=None, nanValue=None, positiveInf=None, negativeInf=None, dateFormat=None, timestampFormat=None, maxColumns=None, maxCharsPerColumn=None, maxMalformedLogPerPartition=None, mode=None, columnNameOfCorruptRecord=None, multiLine=None, charToEscapeQuoteEscaping=None)
#path 地址的字符串，或者字符串列表,或者储存在csv行中的rdd字符串 
#schema 输入图表的类型，可以是一个pyspark.sql.types.StructType或者DDL格式的字符串
#sep 设置分隔符，如果没有设置，默认为','
#encoding csv文件的编码格式。默认为UTF-8
#quote – 设置用于引用带转义字符的值的符号。 如果None设置，它将使用默认值 "。如果您想关闭引号，则需要设置一个空字符串。
#escape 在已引用的值内设置用于转义引号的单个字符。 如果设置无，则使用默认值\。
#comment 设置一个用于跳过以此字符开头的行的单个字符。 默认情况下（无），它被禁用。
#header 使用第一行作为列的名称。 如果设置为None，则使用默认值false。
#inferSchema 从数据中自动推断输入类型。 它需要额外的数据传递。 如果设置为None，则使用默认值false。
#ignoreLeadingWhiteSpace 是否跳过空格，默认为False
#ignoreTrailingWhiteSpace 是否删除空格。默认为False
#nullValue 设置空值的字符串表示。 如果设置无，则使用默认值，即空字符串。 从2.0.1开始，这个nullValue参数应用于所有支持的类型，包括字符串类型。
#nanValue 设置空数字值的字符串表示。 如果设置无，则使用默认值NaN。
#dateFormat 设置指示日期格式的字符串。 自定义日期格式遵循java.text.SimpleDateFormat的格式。 这适用于日期类型。 如果设置为None，则使用默认值yyyy-MM-dd。
#timestampFormat 设置指示时间戳格式的字符串。 自定义日期格式遵循java.text.SimpleDateFormat的格式。 这适用于时间戳类型。 如果设置无，则使用默认值yyyy-MM-dd'T'HH：mm：ss.SSSXXX。
#maxColumns 定义记录可以有多少列的硬限制。 如果设置无，则使用默认值20480。
#maxCharsPerColumn 定义读取任何给定值所允许的最大字符数。 如果None设置，则使用默认值-1，意味着无限长度。
#columnNameOfCorruptRecord 允许重命名由PERMISSIVE模式创建的格式不正确的新字段。 这将重写spark.sql.columnNameOfCorruptRecord。 如果None设置，它将使用spark.sql.columnNameOfCorruptRecord中指定的值。
#multiLine 是否允许解析多行records。 如果设置为None，则使用默认值false。


from pyspark.sql import SparkSession
spark=SparkSession.builder.getOrCreate()
df = spark.read.csv("file:///usr/local/spark/examples/src/main/resources/people.csv")
```

#### parquet

```python
from pyspark.sql import SparkSession
spark=SparkSession.builder.getOrCreate()
df = spark.read.parquet('python/test_support/sql/parquet_partitioned')
df.dtypes
->[('name', 'string'), ('year', 'int'), ('month', 'int'), ('day', 'int')]
```

#### orc

需要有hive支持

```python
from pyspark.sql import SparkSession
spark=SparkSession.builder.getOrCreate()
df = spark.read.orc('python/test_support/sql/orc_partitioned')
df.dtypes
->
[('a', 'bigint'), ('b', 'int'), ('c', 'int')]
```

#### txt

```python
text(paths, wholetext=False)
#paths 输入数据的地址，可为字符串或字符串列表
#wholetext 是否将一个地址中的文件读作一行


from pyspark.sql import SparkSession
spark=SparkSession.builder.getOrCreate()
df = spark.read.text('python/test_support/sql/text-test.txt')
df.collect()
->
[Row(value='hello'), Row(value='this')]
df = spark.read.text('python/test_support/sql/text-test.txt', wholetext=True)
df.collect()
->
[Row(value='hello\nthis')]
```

#### spark.createDataFrame

##### pandas.DataFrame

```python
from pyspark.sql import SparkSession
import pandas
spark=SparkSession.builder.getOrCreate()
df=spark.createDataFrame(pandas.DataFrame([[1, 2]]))
```

##### spark.DataFrame

##### RDD

##### list

#### spark.read.format.load()

一种统一的读取数据的方法		

```python
df = spark.read.format('json').load('python/test_support/sql/people.json')
df.dtypes
->[('age', 'bigint'), ('name', 'string')]
```

### 保存数据

#### write.format().save()

一种统一的保存数据的方法，只要调整format()中的类型参数即可。

```python
df.write.format('json').save(os.path.join(tempfile.mkdtemp(), 'data'))
```

#### csv

```python
csv(path, mode=None, compression=None, sep=None, quote=None, escape=None, header=None, nullValue=None, escapeQuotes=None, quoteAll=None, dateFormat=None, timestampFormat=None, ignoreLeadingWhiteSpace=None, ignoreTrailingWhiteSpace=None, charToEscapeQuoteEscaping=None)
#path 保存的地址
#mode
'''
指定保存操作目标文件已存在时的行为。

append：将此DataFrame的内容追加到现有数据。
overwrite：覆盖现有数据。
ignore：如果数据已经存在，则默认忽略此操作。
error or errorifexists (default case)：如果数据已经存在，则抛出异常。
'''
#compression 压缩编解码器以在保存到文件时使用。 这可以是以下不区分大小写缩写名称中的一个（none，bzip2，gzip，lz4，snappy和deflate）。


df.write.csv(os.path.join(tempfile.mkdtemp(), 'data'))
```

#### json

```python
json(path, mode=None, compression=None, dateFormat=None, timestampFormat=None)
#path 保存路径

df.write.json(os.path.join(tempfile.mkdtemp(), 'data'))
```

### df数据操作

#### collect()

将所有数据以行列表的形式返回

```python
df.collect()
```

#### printSchema()

#### 以树形模式展示schema

```python
df.printSchema()
```

#### select()

选取特定的设置并返回新的df

```python
df.select('*').collect()
->[Row(age=2, name='Alice'), Row(age=5, name='Bob')]
df.select('name', 'age').collect()
->[Row(name='Alice', age=2), Row(name='Bob', age=5)]
df.select(df.name, (df.age + 10).alias('age')).collect()
->[Row(name='Alice', age=12), Row(name='Bob', age=15)]
```

#### alias()

返回列的新名字

```python
alias(*alias, **kwargs)
#alias 列的新名称
#metadata 一个信息字典存储在相应的元数据属性中

>>> df.select(df.age.alias("age2")).collect()
[Row(age2=2), Row(age2=5)]
>>> df.select(df.age.alias("age3", metadata={'max': 99})).schema['age3'].metadata['max']
99
```

#### show()

展示前n行数据

```python
show(n=20, truncate=True, vertical=False)	
#n 显示的行数
#truncate 如果设置为True，则默认截断超过20个字符的字符串。 如果设置为大于1的数字，则截断长字符串以截断长度并将其右对齐。
#vertical 如果设置为True，则垂直打印输出行（每列值一行）。

df
->DataFrame[age: int, name: string]
df.show()
+---+-----+
|age| name|
+---+-----+
|  2|Alice|
|  5|  Bob|
+---+-----+
df.show(truncate=3)
+---+----+
|age|name|
+---+----+
|  2| Ali|
|  5| Bob|
+---+----+
df.show(vertical=True)
-RECORD 0-----
 age  | 2
 name | Alice
-RECORD 1-----
 age  | 5
 name | Bob
```

#### filter()/where()

条件选取

```python
#可以使用types.BooleanType类型的列，或者sql的表达式字符串
>>> df.filter(df.age > 3).collect()
[Row(age=5, name='Bob')]
>>> df.where(df.age == 2).collect()
[Row(age=2, name='Alice')]
>>> df.filter("age > 3").collect()
[Row(age=5, name='Bob')]
>>> df.where("age = 2").collect()
[Row(age=2, name='Alice')]
```

#### groupBy()/groupby()

按照特定的列进行分组

```python
>>> df.groupBy().avg().collect()
[Row(avg(age)=3.5)]
>>> sorted(df.groupBy('name').agg({'age': 'mean'}).collect())
[Row(name='Alice', avg(age)=2.0), Row(name='Bob', avg(age)=5.0)]
>>> sorted(df.groupBy(df.name).avg().collect())
[Row(name='Alice', avg(age)=2.0), Row(name='Bob', avg(age)=5.0)]
>>> sorted(df.groupBy(['name', df.age]).count().collect())
[Row(name='Alice', age=2, count=1), Row(name='Bob', age=5, count=1)]
```

### dfsql化

#### createOrReplaceTempView

将df转换为可以sql查询。

Spark SQL中的临时视图是会话范围的，如果创建会话的会话终止，将会消失。

```python
#将表变为可sql查询
df.createOrReplaceTempView("people")

#使用sql查询，返回df格式的数据
sqlDF = spark.sql("SELECT * FROM people")
sqlDF.show()
# +----+-------+
# | age|   name|
# +----+-------+
# |null|Michael|
# |  30|   Andy|
# |  19| Justin|
# +----+-------+
```

#### createGlobalTempView

创造全局的临时视图，当程序终结时才会消失。因其与系统保存的数据库global_temp绑定，我们必须使用限定名称`golbal_temp`来引用它，例如， SELECT * FROM global_temp.view1。

```python
df.createGlobalTempView("people")

# Global temporary view is tied to a system preserved database `global_temp`
spark.sql("SELECT * FROM global_temp.people").show()
# +----+-------+
# | age|   name|
# +----+-------+
# |null|Michael|
# |  30|   Andy|
# |  19| Justin|
# +----+-------+

# Global temporary view is cross-session
spark.newSession().sql("SELECT * FROM global_temp.people").show()
# +----+-------+
# | age|   name|
# +----+-------+
# |null|Michael|
# |  30|   Andy|
# |  19| Justin|
# +----+-------+
```



### 附件

#### pyspark.sql.types.StructType类型列表

| 支持类型      | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| NullType      | 代表空类型，用于无法推断的类型                               |
| StringType    |                                                              |
| BinaryType    | 二进制（字节数组）数据类型。                                 |
| BooleanType   |                                                              |
| DateType      | 日期 (datetime.date) 数据类型                                |
| TimestampType | 时间戳（datetime.datetime）数据类型。                        |
| DecimalType   | 十进制（decimal.Decimal）数据类型。有 （precision=10, scale=0）两个参数，precision表示总位数，scale表示小数点右边的位数。precision最大为38位。 |
| DoubleType    |                                                              |
| FloatType     |                                                              |
| ByteType      |                                                              |
| IntegerType   |                                                              |
| LongType      |                                                              |
| ShortType     |                                                              |
| ArrayType     |                                                              |
| MapType       |                                                              |

