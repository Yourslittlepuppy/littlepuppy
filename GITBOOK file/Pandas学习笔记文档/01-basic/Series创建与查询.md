# 数据分析之Pandas 学习

## Pandas -Series 数据结构

```python 
# 导入库
import numpy as np
import pandas as pd
```

**1 > 创建Series**

-   列表创建  
-   字典创建
-   其他创建

```python
# 列表创建
a = pd.Series([2,3,4,5,6])
b = pd.Series([2,3,4,5,6], index=['a', 'b', 'c', 'd', 'e']) # 自定义索引
c = pd.Series(['张三', 18, 85.5, True])
# 虽然series 可以存储不同类型的数据,但是为了以后方便对数据处理,同一列数据建议保持一致
```

````python
# 字典创建Series
# 索引就是字典的键
d = pd.Series({
    'name': '张三',
    'age': 18,
    'gander': True,
})
# 使用字典创建时会自动将字典的key作为索引,但也可以自定义索引
d2= pd.Series(d2, index=['name', 'age', 'score'])
````

```python
# 其他方式创建Series 
1> pd.Series(5)  #  单个数据
2> pd.Series(5,index=[1,2,3,4]) # 4行都是5 
3> pd.Series(5,index = np.arange(5))
4> pd.Series(range(5))  => pd.Series(np.range(5))
```

**2> Series 的name**

```python 
x = pd.Series(
    np.arange(4),  # 值
    index=np.arange(9, 5, -1),  # 索引
    name='Series名称',
)
>> x # 打印
>> x.name # 输出 Series 的名字
>> x.name = '修改后的Series 名称' # 就地修改
>> x.name # 已经被修改
```

## Series 数据查询

**1> 整体查询**


```python
S = pd.Series([27.2, 27.65, 27.70, 28, 28, np.nan])
```

| 操作             | 含义                                                         |
| ---------------- | ------------------------------------------------------------ |
| S.dtype          | Series的整体数据类型(最大限度包含 int 与float 一起输出float) |
| S.shape          | Series的形状(每个维度的值个数，用元组表示)                   |
| S.count()        | Series里 非空 的元素个数                                     |
| len(S)           | Series里的元素个数，部分同上                                 |
| S.value_counts() | 统计Series里 非空 元素值的出现次数，并自动降序排序           |
| S.unique()       | Series里 不重复 的元素（去重）                               |

**2> 查询(values) 和索引(index)**
一个Series 数据由2 个nparray 数组组成的

```python
class1 = pd.Series([95, 25, 59, 90, 61], index=['ming', 'hua', 'hong', 'huang', 'bai'])
>> class1
>> class1.values() # 输出所有的值
>> class1.index   # (type(class.index)) ==> pandas.core.indexes.base.Index
>> class1.index.values    # numpy.ndarray
# 结果返回的是numpy.ndarray数据类型
>> class1.index.values[1] # 查询单个索引值
```

**3> 查询值**

-   根据索引查询值
    -   索引查询
    -   切片查询
-   根据调剂反查索引
    -   布尔查询

```python
class1 = pd.Series([95, 25, 59, 90, 61], index=['ming', 'hua', 'hong', 'huang', 'bai'])
>> class1.hong
>> class1['hone']
>> class1[2] # 利用Series 的默认索引查找
>> class1[['hua','bai']]# 多值查找
>> class1[[1,4]]
>> class1[1:4] class1[:2],class1[1:]
>> class1['hong':'bai']
# 注意切片查询是前包后不包的
>> class1[[False, True, True, False, False]] # 返回为True 的值
>> class1 < 60  # 自动生成布尔查询条件
>> class1[class1 < 60] # 组合使用查询 
```

**4> Pandas 的向量化运算**

```python 
# 1> 整体加减 
class1 +5 #给所有值都加5
# 2> 函数
class1.sum()/len(class1) # 总分除以总人数,得平均数
class1.mean() #平均数
np.mean(a)# 平均数
```

**5> get 与 in  操作**

```python 
'ming' in class1 # 存在则返回True 不存在返回返回False
class1.get('ming',60) # 与字典类似,存在则返回,不存在则返回缺省值
```

**6>修改值**

```python 
# 修改单值
class1['hua'] = 15 # 先查后改
#修改多值
class1[['ming', 'hua']] = [98, 12]
# 修改索引
class1.index.values[0] = 'xiaoming' 
# 以上修改都是就地修改.原始数据会被修改,不建议使用
class1copy = class1.rename({'xiaoming':'小明','hong':'小红'})
# 通过字典的方式修改需要改动的索引
```



  

