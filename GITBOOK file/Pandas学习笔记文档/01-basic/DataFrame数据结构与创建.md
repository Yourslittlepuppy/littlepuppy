# Pandas - DataFrame数据结构

---

DataFrame对象是Pandas最常用的数据类型

DataFrame对象是由多个Series增加一个索引后组成的一种表格类型数据结构

## DataFrame对象既有行索引，又有列索引

* 行索引，表明不同行，横向索引，叫index，0轴，axis=0
* 列索引，表名不同列，纵向索引，叫columns，1轴，axis=1

```python
# 导库
import numpy as np
import pandas as pd
```

###  一,创建

* 列表创建
    * ndarray数组创建
* 字典创建
    * 字典内嵌套列表：要求内部列表等长
    * 字典内嵌套字典：内部字典不需要等长
        * 字典内嵌套Series：等同嵌套字典

```python
1> 列表创建
# 默认索引
pd.DataFrame([[1,2,3],[4,5,6],[7,8,9]]) # 使用默认索引
# 自定义索引
pd.DataFrame(
    [[1,2,3],[4,5,6],[7,8,9]],
    index=['a', 'b', 'c'],  # 行索引
    columns=['d', 'e', 'f']  # 列索引
)
```

#### 使用Numpy数组创建

Numpy数组类似一个高级版的Python列表

```python 
pd.DataFrame(
    np.arange(10).reshape(2, 5),  # 生成 2行5列
    index=[1,2], # 行所以呢
    columns=['a', 'b', 'c', 'd', 'e'] # 列索引
)
```

#### 字典内嵌套列表

列表值需要等长，否则报错

#### 示例：将2维表格数据转为DataFrame

| 编号 | 姓名 | 年龄 | 性别  | 分数 |
| ---- | ---- | ---- | ----- | ---- |
| 1    | 张三 | 18   | True  | 85.5 |
| 2    | 李四 | 28   | True  | 59   |
| 3    | 王五 | 38   | False | 78   |

```python 
pd.DataFrame(
    {
        '姓名': ['张三', '李四', '王五'],
        '年龄': [18, 28, 38],
        '性别': [True, True, False],
        '分数': [85.5, 59, 78],
    }
)
##########################1
pd.DataFrame(
    {
        '姓名': ['张三', '李四', '王五'],
        '年龄': [18, 28, 38],
        '性别': [True, True, False],
        '分数': [85.5, 59, 78],
    },
    index=[1,2,3],  # 自定义行索引,但索引需要和原字典索引对应
    columns=['姓名', '年龄', '地址', '分数'],  # 自定义列索引
)
```

用字典创建表,且给定行索引,但字典内数据不等长会如何?

```python 
pd.DataFrame(
    {
        'name': {1: '张三', 2: '李四', 3: '王五'},
        'age': {1: 18, 2: 28, 3: 38},
        'gender': {1: True, 2: True, 3: False},
        'grade': {1: 85.5, 3: 78},  # 不等长也不报错
    }
)# 数据不等长是就会自动将对应的单云格变为NaN类型,并不会报错
```

|| name | age  | gender | grade |
| ---- | ---- | ------ | ----- | ---- |
| 1    | 张三 | 18     | True  | 85.5 |
| 2    | 李四 | 28     | True  | NaN  |
| 3    | 王五 | 38     | False | 78.0 |

#### 字典嵌套Series

Series组成的字典 创建DataFrame，同嵌套字典
```python 
# 外层键是列索引，内层键是行索引
pd.DataFrame(
    {
        'name': pd.Series(['张三', '李四', '王五'], index=[1,2,3]),
        'age': pd.Series([18, 28, 38], index=[1,2,3]),
        'gender': pd.Series([True, True, False], index=[1,2,3]),
        'grade': pd.Series([85.5, 78], index=[1,3]),  # 长度不一致不报错
    },
)
```

#### 直接自定义字典或Series自带的行列索引，相当于按索引查询(了解)

使用index/columns属性自定义已有索引的DF的索引，相当于查询（已有的会查到，没有的缺失值）

```python 
h_values = {
    'name':pd.Series(['小明', '小华', '小红', '小靑', '小兰'], index=[1, 2, 3, 4, 5]),
    'sex':pd.Series([1, 0, 0, 1, 0], index=[1, 2, 3, 4, 5]),
    'age':pd.Series([28, 38, 48, 8], index=[2, 3, 4, 5])  # 少一个值自动填充为NaN
}
1> >> pd.DataFrame(h_values)  # age 的第一个单元格为NaN
2> >> 
# 自定义已有的行列索引
# 字典或Series类型自带的索引，直接修改，相当于查询
pd.DataFrame(
    h_values,
    index=[1,2,3,6,9],
    columns=['name', 'age', 'address']
)
# H_values中存在的则查找并显示,但是不存在的则新增并给其值赋于为NaN
```

|| name | age  | address |
| ---- | ---- | ------- | ---- |
| 1    | 小明 | NaN     | NaN  |
| 2    | 小华 | 28.0    | NaN  |
| 3    | 小红 | 38.0    | NaN  |
| 6    | NaN  | NaN     | NaN  |
| 9    | NaN  | NaN     | NaN  |










