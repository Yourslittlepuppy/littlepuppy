# Pandas数据存取

---

数据的输入和输出是Pandas的基础操作

Pandas可以存取多种介质类型数据：常见的有：

* 文本类数据
    * **csv**
    * JSON
* 二进制磁盘数据
    * **Excel**
    * pkl
    * HDF5
* 数据库
    * SQL（略）
* Web API数据
    * HTML
* 其他
    * 内存

---

```python
import numpy as np
import pandas as pd
```

测试数据


```python
av = [
    ['小明','male',18,170,60,'北京海淀',61],
    ['小华','female',28,160,50,'上海静安',74],
    ['小红','female',22,175,64,'广州天河',59],
    ['小靑','male',31,182,80,'深圳南山',82],
    ['小兰','female',25,165,55,'杭州西湖',98],
]
a = pd.DataFrame(
    av,
    index=[1,2,3,4,5],
    columns=['name','sex','age','heigh','weight','address','grade']
)
```

## Pandas 存取CSV

**CSV|**
Comma-Separated Values，逗号分隔值
以纯文本形式存储表格数据的一种格式
二维表格数据结构

CSV是一种简单、通用的文件格式，常用于在不同程序和环境之间中转表格数据， 
这些程序本身是在不兼容的格式上进行操作的（往往是私有的和/或无规范的格式）， 
基本所有数据类软件和环境都支持读写CSV文件

    * csv
        * 二维数据格式
        * 精简，省空间
        * 主要用于数据领域
    * JSON
        * 多维数据格式
        * 冗余，费空间
        * 主要用于编程、数据交换领域

```python
# 读取CSV文件
pd.read_csv('data/ceshi.csv', encoding='GBK') # 最好是指定编码
```

```python
# 保存csv文件，默认utf8格式
a.to_csv('data/foo.csv')
a.to_csv('data/foo.csv',encoding = 'GBK')
```

### 进阶：CSV写入和读取的常见参数设置


```python
a.to_csv(
    'data/foo3.csv',
    index=False,  # 不保存行索引*****
#     header=False,  # 不保存列索引，不推荐
    columns=['name', 'address', 'grade'],  # 只保存指定列
)
```

### 读取CSV常见设置

```python
a3 = pd.read_csv(
    'data/foo.csv',  # 读取文件路径
    sep=',', # 指定分隔符，csv默认逗号，如果是table表格数据一般为 \t
    
    # 列索引
#     header=0, # 列索引，默认0将第一行设为表头（其他行号也可），
#     header=None,  # None不将第一行设为表头（列索引），
#     header=[0,1,2],  # [0,1]列表可将多行同时设为表头（层次化索引）
#     names=['x','姓名', '性别', '年龄', '身高', '体重', '地址', '成绩'],  # 配合header=0，自定义列索引
    
    # 行索引
#     index_col=None,  # 行索引，默认值None：不使用数据列，而是使用系统自带索引
#     index_col=0,  # 把第0列作为行索引
#     index_col='name',  # （自定义列 作为行索引），
#     index_col=[0,1,2],  # 默认索引，多列 层次化索引
#     index_col=['name','sex','age'],  # 自定义索引，多列

    #  读取指定的行和列
#     usecols=[0,2,4],  # 读取指定列，默认索引
#     usecols=['name', 'address', 'grade'],  # 读取指定列，自定义索引
#     nrows=3,  # 读取前几行***********************
#     skiprows=3,  # 从表格开始算起，忽略的行
#     skiprows=[2,4],  # [2,4]跳过文件第2/4行
#     skipfooter=2,  # 从表格末尾算起忽略的行，必须配合engine='python'否则会报警告
#     engine='python',  # 引擎。c更快，python更完善
    
#     替换空值
#     na_values=['male'],  # 将csv中某些字符替换为空值
#     keep_default_na=False,  # 默认True同时使用系统自带空值替换和自定义空值，如 NA,N/A等，False只使用自定义空值

    encoding='utf-8',  # 编码，默认utf-8,引擎是python时需要手动设置
)
a3
```


```python
a33 = pd.read_csv('data/foo.csv', header=[0,1,2])
a33['name']['小明']['小华'][0]
```


```python
a34 = pd.read_csv('data/foo.csv', index_col=[0,1,2])
a34.loc[2].loc['小华'].loc['female']['address']
```

### 合并时间列及自定义某列为行索引

多用于时间序列，金融数据分析

**参数: parse_dates:**
* 尝试将数据解析为日期
* 可以使用列表指定需要解析的一组列名，如果列表元素为字典包含的列表或元组，会将多个列组合到一起再解析日期解析（如日期和时间分别在两个列的情况

```python
数据：aaa.csv

    data,time,time2,name,age
    20100101,000000,00:00:00,"张三",18
    20100101,230000,23:00:00,"李,四",28
```


```python
t2 = pd.read_csv(
    'data/aaa.csv',
#     parse_dates=['data'],
    parse_dates=['data','time','time2'],
    # parse_dates={'s': ['data', 'time2']}, # 将作为时间的列
    keep_date_col=True,  # 保留合并前的列
    # index_col='s',  # 指定某列作为行索引
    encoding='GBK'
)
```

| |data | time       | time2  | name                | age   |
| ---- | ---------- | ------ | ------------------- | ----- | ---- |
| 0    | 2010-01-01 | 000000 | 2019-08-03 00:00:00 | 张三  | 18   |
| 1    | 2010-01-01 | 230000 | 2019-08-03 23:00:00 | 李,四 | 28   |

上面的时间还是有些不正确,比如time2的时间是按照当前时间进行转化,与data时间有出入,该怎么解决呢?

```python
t2 = pd.read_csv(
    'data/aaa.csv',
#     parse_dates=['data'],
#   parse_dates=['data','time','time2'],
    parse_dates={'s': ['data', 'time2']}, # 将作为时间的列
    keep_date_col=True,  # 保留合并前的列
    index_col='s',  # 指定某列作为行索引
    encoding='GBK'
)
```

## Pandas存取Excel（xlsx）

```python 
df.to_excel('data/foo.xlsx')  # 默认存入工作表Sheet1
df.to_excel('data/foo.xlsx', 'abc')  # 自定义工作表表名
df.to_excel('data/foo.xlsx', sheet_name='abc')  # 同上
```

将多个变量写入同一Excel多个工作表中.

```python
# 完整写法
# 创建表格
writer = pd.ExcelWriter('data/output.xlsx')

# 插入一个表
df.to_excel(
    writer,
    'abc',
)

# 插入第二个表
df.to_excel(
    writer,
    'Sheet1',  # 工作表表名
    index=None,  # 不要行索引
    header=None,  # 不要列索引
)

# 写入
writer.save()
```

```python
pd.read_excel('data/output.xlsx')  # 默认读取第一个工作表
pd.read_excel('data/output.xlsx', 'Sheet1').head(2)  # 读取指定的工作表 由于没有必要读出全部数据,可以先读出前面的几条作为参考.看是否符合条件.
e = pd.read_excel('data/output.xlsx', None) # none 表示读取全部的数据表,并返回一个有序字典.通过索引可以提前数据
e['abc']
e['Sheet1'].head()
```

复杂读取参数

```python 
x = pd.read_excel(
    'data/output.xlsx',
    
#     sheet_name=1,  # 工作表名，默认第一个工作表(索引为0),注意新版pandas是sheet_name,
#     sheet_name=[0,1],  # 选中的多个工作表
    sheet_name=None,  # 所有的工作表

#     header=None,  # 不使用行做列索引
#     header=1,  # 使用某行做列索引
#     header=[0,1,2],  # 多行做层次化索引
    
#     index_col=1,  # 不设行索引，多个[1,2,3]层次化索引
    
#     nrows=3,  # 读取前多少行(pandas0.23.0新版才支持)
#     skiprows=[2,4],  # 跳过的行，从表起始位置算，[2,4]列表参数也可
#     skipfooter=2,  # 跳过的行，从表结束位置算，默认为0，不能用列表

#     na_values='male',# 表示为NAN,na_values可以用一个列表或集合的字符串表示缺失值
#     keep_default_na=True, # 默认True在系统自带空值替换符后追加NA值，如 NA,N/A等，False只使用自定义空值
)
x
x['abc']
```

JSON
---

JSON（JavaScript Object Notation）是通过HTTP请求在Web浏览器和其他应用程序之间发送数据的标准格式之一

JSON和CSV比较：

    JSON是多维数据文件，CSV是二维数据文件
    JSON数据比较冗余，体积较大，CSV精简，体积小
    JSON多用于WEB数据交互，CSV多用于数据领域不同环境切换的数据交互

如果JSON数据格式维度超过2维，转为DataFrame后，只能将0/1维转为表格，其他维度的JSON会以Python字典格式存入表格单元格

```python
a.to_json('data/foo.json')
pd.read_json('data/foo.json')
```

## Pandas存取pkl

pkl是Python专有的二进制数据存储格式，可以存储原生的Python数据类型

```python
a.to_pickle('data/foo.pkl')
y = pd.read_pickle('data/foo.pkl')

```

使用HDF5格式
---

科学领域大数据存储的通行标准，如天文、物理、地球科学等

```python
#创建一个类科学计数数据
frame = pd.DataFrame({'a': np.random.randn(100)})
frame.loc[:10]
# 存储
frame0.to_hdf('data/mydata.h5', 'obj1') #写入时,给存入的数据加一个变量名
frame1.to_hdf('data/mydata.h5', 'obj2') # 在创建一份不同的数据,在次写入
frame2.to_hdf('data/mydata.h5', 'test01')
# 读取
# x = pd.read_hdf('data/mydata.h5')  # 文件内只存储了一个变量,如果多个变量读取出错,上面定义了三个变量,读取是也应该加上对应的变量名
x = pd.read_hdf('data/mydata.h5', 'obj1')  # 指定变量名输出
x = pd.read_hdf('data/mydata.h5', 'test01')  # 指定变量名输出
x.loc[:10]
```

```python 
# 设置存储模式，默认fixed
frame.to_hdf('data/mydata2.h5', 'obj1', format='table') #  fixed 模式下存储的文件大小要小于 table 模式
'------------------------------'
# 高级查询模式，只能在table模式下实现
x = pd.read_hdf('data/mydata3.h5','obj1', where=['index < 5 and index > 1'])
```

复杂应用

```python
# 创建或读取HDF5文件（没有就创建，有就读取）
store = pd.HDFStore('data/mydata3.h5')
store
>>> 
<class 'pandas.io.pytables.HDFStore'>
File path: data/mydata3.h5
# # 插入数据到h5
store['obj1'] = frame
# 继续插入
store['obj1_col'] = frame['a']
#'--------------------------'
# 读取数据
store['obj1']  # 读取取的是一个数据表
store['obj1_col'][:10] # series 数据类型,与存入的一致
```

```python
# put是直接赋值的显式版本，可以自定义格式化格式
store.put('obj2', frame, format='table')
# select是高级版读取，可以增加查询条件
store.select('obj2', where=['index >=10 and index <= 20'])# 读取指定的索引号
```

XML和HTML：Web信息收集
---

网页中有多个表格的，返回列表，可按列表索引逐个输出

* 有些网站由于网络原因、反爬虫、服务器原因导致无法抓取网页内容，400、500错误，这时可以将网页另存到本地后读取
* 有些网站的表格HTML结构复杂混乱，导致解析表格结构出错，只能手动处理

```python 
# 网页爬取
table = pd.read_html('http://www.stats.gov.cn/tjsj/zxfb/201806/t20180630_1607071.html')  # 某些网络下无法抓取
# table = pd.read_html('data/2018年6月中国采购经理指数运行情况.html')  # 某些网络下无法抓取
# 通过索引可以提取网页页面的表格.不存在是则会报错, not defiend
table[0]
table[1]
table[2]
table[3]
table[4]
```

```python
# 网址复制到内存读取数据
#  https://cn.investing.com/currencies/usd-cny-historical-data
pd.read_clipboard(engine='python')
# 直接从网页选中复制,然后到python中运行函数.这里将会从内存中读取表格
pd.read_clipboard(header=None, engine='python')  # 不将第一行设为表头
# 如果原本表格不是严格的表格,复制到jupyter中将会失真,表格不能完整创建
```

