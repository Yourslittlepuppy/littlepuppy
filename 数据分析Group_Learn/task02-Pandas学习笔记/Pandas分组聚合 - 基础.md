Pandas分组聚合 - 基础
===

---

数据分析阶段：

数据规整（清洗）阶段后，下一阶段就是 分组聚合

对数据集分组并对各组应用一个函数是数据分析中的重要环节
一般将数据准备好后，首先就是计算分组统计
sql能够方便的连接、过滤、转换和聚合数据，但sql能执行的分组运算种类有限，Pandas则强大灵活的多

```python
import numpy as np
import pandas as pd
```

```python 
#测试数据
class1 = pd.DataFrame({
    'name': ['张三','李四','王五','李四','王五','王五','赵六'],
    'chinese': [18, 53, 67, 63, 39, 70, 94],
    'math': [82, 63, 41, 59, 46, 39, 58],
    'english': [68, 52, 90, 86, 60, 98, 64],
    'test': ['一','一','一','二','二','三','一']
})
class1
```

| |name | chinese | math | english | testnum |
| ---- | ------- | ---- | ------- | ---- | ---- |
| 0    | 张三    | 18   | 82      | 68   | 一   |
| 1    | 李四    | 53   | 63      | 52   | 一   |
| 2    | 王五    | 67   | 41      | 90   | 一   |
| 3    | 李四    | 63   | 59      | 86   | 二   |
| 4    | 王五    | 39   | 46      | 60   | 二   |
| 5    | 王五    | 70   | 39      | 98   | 三   |
| 6    | 赵六    | 94   | 58      | 64   | 一   |

## 数据聚合


一般指应用某些方法（自定义的聚合函数或系统自带Pandas的统计方法等）给数据**降维**


```python
class1.sum() #给表中所以行中数据都相加,如果是同一类型的则按用类型相加,或者按字符串相加 ,不能相加的则过滤掉
```

## 数据分组

分组聚合：groupby()，一般指以下一个或多个操作步骤的集合

* Splitting **分组**：将列分割为n组
* Applying **应用**：给每个分组应用聚合函数
* Combining **合并**：合并分组和聚合的结果

![mark](http://qiniu.lizhaoxi.cn/blog/20190805/URxbsQR0b9JU.png?imageslim)


```python
# 分组
a = df.groupby('name')
print(a)
>> ## <pandas.core.groupby.generic.DataFrameGroupBy object at 0x000000000E8DBC88>
print(type(a))
>> # <class 'pandas.core.groupby.generic.DataFrameGroupBy'>
```

常用的聚合函数:
* 平均值：.mean() （透视表）
* 个数：.size()  （交叉表）

举例: 求每位同学,每科成绩的平均分

```python
result =class1.groupby('name').mean() # 先进行分组,然后进行聚合
```

举例: 求每位同学的考试次数:

```python
result = class1.groupby('name').size() 
```

```python
如果不想使用分组列作为索引，设置参数as_index=False
df.groupby('name', as_index=False).mean()
```

| |name | chinese | math      | english |
| ---- | ------- | --------- | ------- | --------- |
| 0    | 张三    | 18.000000 | 82.0    | 68.000000 |
| 1    | 李四    | 58.000000 | 61.0    | 69.000000 |
| 2    | 王五    | 58.666667 | 42.0    | 82.666667 |
| 3    | 赵六    | 94.000000 | 58.0    | 64.000000 |

上面是以单列为基准分组，聚合其他所有列

下面是：

* 多列分组
* 单列或多列聚合

---

## 同时以多列为基准分组

### 多列分组

```python
x2 = df.groupby(['name', 'test']).mean()
x2
x3= df.groupby(['name','test'],as_index = False).mean()
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/8VlvzmpASJxI.png?imageslim)

### 单列或多列聚合

对于大数据集，很可能只需要对部分列进行聚合

下列三种写法结果一样
	分组聚合参数，传入标量形式的单个列名，返回Series
	分组聚合参数，传入列表或数组，返回DataFrame（默认传入所有列）

```python
#对除分组基准列以外的所有列进行聚合
df.groupby('name').sum()
```
分组后，只对 chinese 列进行聚合

```python
# 方法1：对所有列进行聚合后，抽取需要的列
# 效率低（会运算所有列)，不推荐
df.groupby('name').sum()['chinese']

###############

# 方法2：抽出聚合列，和分组基准列，单独运算
# 效率高，但写着麻烦
df['name']
df['chinese']

# 传入标量或列名，返回Series
df['chinese'].groupby(df['name']).sum()

###############

# 方法3：分组后指定某列进行聚合，书写简单，效率高，推荐**********************
# 是上面写法的简写（语法糖）
df.groupby('name')['chinese'].sum()
```

多列和指定列聚合

```python
df.groupby('name').sum()  # 聚合除分组列外的所有列
df.groupby('name')['chinese'].sum()  # 聚合1列
df.groupby('name')['chinese', 'math'].sum()  # 聚合指定多列
```

聚合列，传入参数不是单值，而是列表或数组，返回DataFrame（了解）


```python
df.groupby('name')['chinese'].mean()  # 返回Series
df.groupby('name')[['chinese']].mean()  # 返回DataFrame  # 这种方法最好
df[['chinese']].groupby(df['name']).mean()
df.groupby('name').mean()[['chinese']]
```

Pandas分组聚合 - 进阶
===

---

本质上，groupby传入的数据并不是行索引或列索引，而是任意一个和**数据结构对应**的序列（布尔值序列、列表、数组、字典、Series）

* 根据列表做分组基准
* 根据字典或Series做分组基准
* 根据函数做分组基准
* 根据层次化索引的级别做分组基准

---

* 按行分组
* 按列分组


```python
#根据列表进行分组
#列表值个数必须和表格行或列数对应
df.groupby('name').size()  # 分组参数是列索引，直接获取某列的值
df.groupby(df['name']).size()  # 手动将列值传入分组参数
# 手动构建列表,传入分组基准
aaa = ['张三', '李四', '王五', '李四', '王五', '王五', '张三']  # 赵六变成张三 
df.groupby(aaa).size()   # 没有找到的则不返回结果
张三    1
李四    2
王五    3
```

### 根据字典或Series分组
---

字典会对应替换表格的行或列**索引值**，然后进行分组


```python
df2 = df.set_index('name')  # 将name列转为行索引
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/THkvIaUkbu4m.png?imageslim)


```python
# 行索引，张三、赵六合并为“考一次”，李四改为‘考两次’,王五不在字典中，过滤掉
mapping2 = {'张三': '考一次', '李四': '考两次', '赵六': '考一次'}
df2.groupby(mapping2).sum()

       chinese math english
考一次  112     140   132 
考两次  116     122   138  
# 字典首先会对表格进行索引值替换然后在对该索引进行分组
```

使用Series进行分组，同字典

```python
mapping3 = pd.Series(mapping2)
mapping3
df2.groupby(mapping3).sum()
```

### 通过函数分组
---

比起字典或Series，函数是一种更原生的方法定义分组映射

任何被当做分组键的函数都会在各个**索引值**上被调用一次，其返回值就会被用作分组基准

```python
# 测试数据
people = pd.DataFrame(
    np.random.randn(5, 5),
    columns=['a', 'bc', 'c', 'd', 'e'],
    index=['Joe', 'Steve', 'Wes', 'Jim', 'Travis']
)
people.iloc[2:3, [1, 2]] = np.nan
people
```

| |a      | bc        | c         | d         | e        |
| ------ | --------- | --------- | --------- | -------- | --------- |
| Joe    | -0.664380 | -0.605555 | -0.650991 | 0.512568 | 0.262389  |
| Steve  | -0.582732 | 1.368422  | 0.053369  | 1.490574 | 0.756367  |
| Wes    | 0.469641  | NaN       | NaN       | 1.783701 | -0.350301 |
| Jim    | -0.989650 | -0.634254 | 1.898762  | 1.996523 | -1.118672 |
| Travis | 2.180328  | 0.190467  | 1.261686  | 0.417660 | 0.893958  |

```python
len('Joe'), len('Steve'), len('Travis') # 返回的是字符串长度
```


```python
# 通过函数进行分组
# 1> 按照索引长度
people.groupby(len).sum()
# 按照索引字符串长度进行分组
# 手动将分组基准插入，结果同上
a = [3,5,3,3,6]
people.groupby(a).sum()
```

|      | a         | bc        | c         | d        | e         |
| ---- | --------- | --------- | --------- | -------- | --------- |
| 3    | 2.209750  | -0.421323 | -1.410263 | 2.504427 | -0.229839 |
| 5    | -0.720086 | 0.265512  | 0.108549  | 0.004291 | -0.174600 |
| 6    | -1.977728 | -1.743372 | 0.266070  | 2.384967 | 1.123691  |

### 各种分组基准结合


```python
ccc = ['one', 'one', 'one', 'two', 'two']
people.groupby([len, ccc]).sum()
# 对上面进行变形
people['f']=['one', 'one', 'one', 'two', 'two'] # 手动增加一列
people.groupby([len,'f']).sum() #  分组后的结果与上面类似,只是多了一个索引.
```

|      |          | a         | bc        | c         | d         | e         |
| ---- | -------- | --------- | --------- | --------- | --------- | --------- |
| 3    | one      | 1.764613  | 0.715279  | -1.545400 | 1.019890  | 0.849966  |
| |two  | 0.445138 | -1.136602 | 0.135137  | 1.484537  | -1.079805 |
| 5    | one      | -0.720086 | 0.265512  | 0.108549  | 0.004291  | -0.174600 |
| 6    | two      | -1.977728 | -1.743372 | 0.266070  | 2.384967  | 1.123691  |

### 根据层次化索引的级别分组
---

要根据层次化索引的级别分组，使用level关键字传递级别序号或名字

```python
columns = pd.MultiIndex.from_arrays([['US', 'US', 'US', 'JP', 'JP'], [1, 3, 5, 1, 3]],  names=['cty', 'tenor'])
hier_df = pd.DataFrame(np.random.randn(4, 5), columns=columns)
hier_df
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/lAGR0XR0JS6c.png?imageslim)

```python
hier_df.index  # RangeIndex(start=0, stop=4, step=1)
hier_df.columns # 列索引是一个多层index
>>
MultiIndex([('US', 1),
            ('US', 3),
            ('US', 5),
            ('JP', 1),
            ('JP', 3)],
           names=['cty', 'tenor'])
```

```python
hier_df.groupby(level='cty', axis=1).sum()
```

| cty  | JP        | US        |
| ---- | --------- | --------- |
| 0    | 2.011399  | -1.436255 |
| 1    | 2.254011  | 1.967562  |
| 2    | 2.610676  | -2.194504 |
| 3    | -1.833159 | -0.913857 |

```python
hier_df.groupby(level='tenor', axis=1).sum()
```

| tenor | 1         | 3         | 5         |
| ----- | --------- | --------- | --------- |
| 0     | 1.401374  | 0.712353  | 1.397996  |
| 1     | -0.743459 | 0.759164  | 0.132708  |
| 2     | 1.451486  | -0.331760 | -0.337632 |
| 3     | 0.170894  | -1.155463 | -0.939433 |

### 按列分组  ---分组后能进行聚合的则进行聚合,不能的则舍弃

```python
# 按行分组
df.groupby('name').sum()  # 简写
df.groupby('name', axis=0).sum()  # 完整
```

使用列数据类型做分组基准,按列分组

```python
df.groupby(df.dtypes, axis=1).sum()
fff = ['object', 'int64', 'int64', 'int64', 'object']
df.groupby(fff, axis=1).sum()
# 返回时会将相同类型的结果返回
```

### 练习：输出每个学生在每次考试次数中的数学平均分
---

从一个原始大表中抽取一个符合需求的小表

```python
df.groupby('name').mean()['math']
df.groupby('name')['math'].mean()
```


```python
每个学生在每次考试次数中数学平均分
df.groupby(['name', 'test'])['math'].mean().unstack().fillna(0).astype(np.int)
# 多列进行分组的进行行转列,空值填充.
```




