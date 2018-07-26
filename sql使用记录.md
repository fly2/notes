## sql使用记录

### oracle表赋权

```sql
GRANT   [select：查询

    insert：插入

    update：更新

    delete：删除

    rule：

    all：所有]

ON   tablename    TO   username

--例子
grant select,insert,update on CC_GETDATA_TO_YYFX to zhongrx
```

### 修改列名

修改输出的列名。**注意：**as后面接的新的列名无需加引号''。

```sql
select t.gender as 性别,
t.age as 年龄
from information t
```

### 字符串拼接

在sql中可以通过||来进行字符串拼接。

```sql
'111'||'222'->'111222'
```

### 排序

排序需要使用order by 搭配desc或者asc desc就是用于查询出结果时候对结果进行排序，是降序排序，而asc就是升序。要用与order by一起用。

```python
select * from student order by id desc
#将查询结果按id从大到小排序

select * from student order by age desc,id desc
#用“,”号隔开多个排序条件，这样，先按age 再按 id，就是说，先按age从大到小排序，如果有相同年龄的，那么相同年龄的学生再按他们的id从大到小排序。
```

### 格式转换

#### to_char

将date格式转换为char格式

```sql
to_char(sysdate,'yyyy-mm-dd hh24:mi:ss')
--sysdate为时间格式，后面字符串为所要输出的字符串格式

select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') time from dual
```

#### to_date

将char转换为日期格式

```sql
to_date('2004-11-27','yyyy-mm-dd')
--前面为字符串，后面为转换的字符串格式

select to_date('20170831','YYYYMMDD') from dual
```

多种日期格式： 

YYYY：四位表示的年份 
YYY，YY，Y：年份的最后三位、两位或一位，缺省为当前世纪 
MM：01~12的月份编号 
MONTH：九个字符表示的月份，右边用空格填补 
MON：三位字符的月份缩写 
WW：一年中的星期 
D：星期中的第几天 
DD：月份中的第几天 
DDD：年所中的第几天 
DAY：九个字符表示的天的全称，右边用空格补齐 
HH，HH12：一天中的第几个小时，12进制表示法 
HH24：一天中的第几个小时，取值为00~23 
MI：一小时中的分钟 
SS：一分钟中的秒 
SSSS：从午夜开始过去的秒数 

#### to_number

将字符串转换成数字。

**注意：**有的时候你会发现，使用了TO_NUMBER()函数并且语法正确，但是Oracle却报“invalid number”的错误，而你在一遍又一遍认认真真检查并确定语句无误之后大呼惊奇，以为TO_NUMBER()函数还有什么可能不知道的用法。其实这很可能是你所查询的数据出现了问题，而非SQL。使用TO_NUMBER()函数的时候，一定要确保所转换字段是可转换为数字的，比如字符串“20151008”是可以转换为数字20151008的，但是字符串“2015-10-08”不可以。如果你的字段中包含了字符串“2015-10-08”，而你还直接使用了TO_NUMBER()函数进行操作的话就会报“invalid number”的错。

```sql
to_number('000012134')
--输入为需要转换为数字的char或者varchar2
```

### 内容转换

将字段中的内容替换为想要的内容,有两种实现方法：

一种是用case ... when ... then 的方法。**注意：**记得写在最后end结束符；

一种是用decode()函数,只可用于oracle中。

```sql
--方法一
CASE sex
         WHEN '1' THEN '男'
         WHEN '2' THEN '女'
ELSE '其他' END
--case 后面接字段名。when后面接需要被替换的内容，then后为替换后的内容。else后接其他未指定的替换后内容。end为结束符。




select t.full_name,
t.birth_date,
case t.gender  when 'M' then '男' 
when 'F' then '女' end  
as 性别 
from SANDBOX_FIXED.CC_TB_INDIVIDUAL t


--方法二
Select decode(列名，值1,翻译值1,值2,翻译值2,...值n,翻译值n,缺省值) From talbename


select t.full_name,
t.birth_date,
decode(t.gender, 'M', '男', 'F', '女',0) as 性别
from SANDBOX_FIXED.CC_TB_INDIVIDUAL t
```

### 字符串抓取

在oracle中可以利用substr()来进行字符串的抓取

```sql
SUBSTR (str, pos, len)
--由 <str> 中的第 <pos> 位置开始，选出接下去的 <len> 个字元。


select t.full_name,
(substr(to_char(t.birth_date,'yyyymm'),1,4)||'年'||substr(to_char(t.birth_date,'yyyymm'),5,2)||'月') ,
case t.gender  when 'M' then '男' 
when 'F' then '女' end  
as 性别 
from SANDBOX_FIXED.CC_TB_INDIVIDUAL t
```

### 分组

将数据进行分组处理，通过group by关键字来执行。分组后的字段不能和普通字段一起select，在select指定的字段要包含在**Group** **By**语句的后面，作为分组的依据；或者被包含在聚合函数中。

```sql
select 
count(1),
t.companycode 
from SANDBOX_FIXED.TB_INTERORGANIZATION t 
group by t.companycode
```

| 函数        | 作用     | 支持性            |
| --------- | ------ | -------------- |
| sum(列名)   | 求和     |                |
| max(列名)   | 最大值    |                |
| min(列名)   | 最小值    |                |
| avg(列名)   | 平均值    |                |
| first(列名) | 第一条记录  | 仅Access支持      |
| last(列名)  | 最后一条记录 | 仅Access支持      |
| count(列名) | 统计记录数  | 注意和count(*)的区别 |

### 分组排序

row_number()函数为从1开始，为每一条分组记录返回一个数字。

```sql
ROW_NUMBER() OVER(PARTITION BY t2.PARTY_ID ORDER BY t2.AG_END_DATE desc)
--将t2按照t2.PARTY_ID进行分组,在分组内按照t2.AG_END_DATE进行倒序排序。
--row_number()表示为分组记录返回数字
--PARTITION BY表示分组所用字段
--ORDER BY表示排列所用字段
--desc表示倒序排列。
```

### 问题记录

#### 一表多字段关联另一张表同一字段

where和inner join不能联用

```sql
--有两种方法，一种是使用多个join进行多次插入，另外一种是在输出时用where进行匹配
--方法一
select t1.*,tt1.dept_name,tt2.companycname
  from sandbox_fixed.cc_tb_lf_t_contract_master t
 inner join sandbox_fixed.TB_INTERORGANIZATION tt2
 on tt2.companycode = t.BRANCH_ID
 inner join sandbox_fixed.TB_LF_T_DEPT tt1
 on tt1.DEPT_ID = t.DEPT_ID
 inner join cc_record_infor t1
 on t1.policycode = t.policy_code
--方法二
create table cc_record_infor1 as
select
t1.*, 
(select tt.companycname from sandbox_fixed.TB_INTERORGANIZATION tt where tt.companycode=t.BRANCH_ID) as 分公司名称,
(select tt.DEPT_NAME from sandbox_fixed.TB_LF_T_DEPT tt where tt.DEPT_ID=t.dept_id ) as 所在小组
from sandbox_fixed.cc_tb_lf_t_contract_master t,
cc_record_infor t1 where t1.policycode=t.policy_code
```

