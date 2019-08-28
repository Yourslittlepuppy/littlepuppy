# Pandas 数据操作

##  Pandas 数据表 命名

### Series 命名用户

* Series的name会变成DataFrame的列索引
* Series的索引name会变成DataFrame的行索引name

```python
import numpy as np
import pandas as pd
```

```python 
a = pd.Series([2,3,4,5,6])
pd.DataFrame(a)
a.name = "名字一"
pd.DataFrame(a)
```

|      | 名字一 |
| ------ | ---- |
| 0      | 2    |
| 1      | 3    |
| 2      | 4    |
| 3      | 5    |
| 4      | 6    |

### DataFrame的命名

```python
# 测试数据
av = [
    ['小明','male',18,170,60,'北京海淀',61],
    ['小华','female',28,160,50,'上海静安',74],
    ['小红','female',22,175,64,'广州天河',59],
    ['小靑','male',31,182,80,'深圳南山',82],
    ['小兰','female',25,165,55,'杭州西湖',98],
]
c = pd.DataFrame(
    av,
    index=[1,2,3,4,5],
    columns=['name','sex','age','heigh','weight','address','grade']
)
c
```

```python
c.index.name = '序号'
c.columns.name='信息'
c
```

| 序号\信息 | name | sex    | age  | heigh | weight | address  | grade |
| ----- | ---- |
| 1    | 小明 | male   | 18   | 170   | 60     | 北京海淀 | 61    |
| 2    | 小华 | female | 28   | 160   | 50     | 上海静安 | 74    |
| 3    | 小红 | female | 22   | 175   | 64     | 广州天河 | 59    |
| 4    | 小靑 | male   | 31   | 182   | 80     | 深圳南山 | 82    |
| 5    | 小兰 | female | 25   | 165   | 55     | 杭州西湖 | 98    |

### 对象遍历

```python
# DataFrame 
for i in c:
    print(i)  # 列索引
#     print(c[i])  # 列值
#     print(type(c[i]))
#     print(c[i][2])  # 输出单行
#     for j in c[i]:
#         print(j)  # 单元格值
#         print(type(j))
用Pandas专有函数遍历
for index, rows in c.iterrows():
    print(index)  # 行索引
#     print(rows)  # 每行
#     print(type(rows))  # 输出单列？
#     for i in rows:
#         print(i)  # 单元格值
#         print(type(i))
```

##  Pandas 数据运算

Python科学计算的基本方式是：向量化运算（矢量化运算），并行运算


###  自定义运算
---

* **apply()**：Series和DataFrame通用自定义运算函数（计算行/列）
* map()：Series用
* applymap()：DataFrame用（计算单元格）

---

    * 如果Pandas库自带的运算和函数不满足需求，可以自定义函数，并同时将函数应用到Pandas的每行／列或值上
    * 应用函数主要用于替代传统的for循环
    * 可以将map或apply的参数x理解为for循环的i值（series就是单值，DataFrame就是每一行(列）值）

针对Series的map函数，会将自定义函数应用到Series对象的每个值

---

### Series的map函数
---

```python
#测试数据:
# 数据
a = pd.Series([9,8,7,6],index=['a','b','c','d'])
b = pd.DataFrame(np.arange(20).reshape(4,5),index=['c','a','d','b']   
```


```python
1> 普通向量化计算
a + 1 - 2 * 3 / 4
2> 自定义函数运算
# def func1(x):
#     return x + 1 - 2 * 3 / 4

func1 = lambda x: x + 1 - 2 * 3 / 4  # lambda表达式，乞丐版函数（匿名函数）
a.map(func1)  # Series使用map进行自定义函数运算
a.apply(func1)  # Series也能使用apply
```

DataFrame的自定义函数操作
---

* apply：操作 行、列
* applymap：操作单元格

```python
 def func2(x):
    return x + 1 - 2 * 3 / 4
b.apply(func2)  # 应用到行
b.applymap(func2)  # 应用到单元格
# 直接应用于单元格的算法，两个函数效果一样
```

不直接应用于单元格的算法

#### 关于行和列的思路

* axis=0 按行操作
    * **给这一列的所有行，进行运算**
* axis=1 按列操作
    * **给这一行的所有列，进行运算**

DataFrame的apply自定义函数应用，复杂，重要

```python
def fun3(x):
    return x.min()  # 返回行、列的最小值

b.apply(func3)  # 默认axis=0,按行运算（每一列的所有行）
b.apply(func3, axis=0)  # 同上
# b.applymap(func3)  # 报错，单元格没有最小值
```

```python
def func4(x):
#     return x.min()
#     return x.min(), x.max()
    # 返回DataFrame表格***************
    return pd.Series([x.min(), x.max()], index=['min', 'max'])  # 构建Series返回，结果就是DataFrame
b.apply(func4)
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190805/xc32gI2LMuBD.png?imageslim)

自定义函数的参数x，用apply调用，在运算过程中表示DataFrame的1行或1列（是Series对象，可以理解为for循环遍历，但它是并行运算)

---

基本统计函数
---

Pandas的统计运算方法，和Numpy基本一致

默认针对0轴（行）做运算，大部分函数可加参数 axis=1 改为对列运算

| 函数 | 解释 |
| ---- | ---- |
|.describe()|	针对0轴的统计汇总，计数/平均值/标准差/最小值/四分位数/最大值|
|.sum()	|计算数据的总和,按0轴计算(各行计算),下同,要按列计算参数1|
|.count()|	非NaN值数量|
|.mean() .median() .mode()|	计算数据的算数平均值/中位数/众数|
|.var() .std()|	计算数据的方差/标准差|
|.min() .max()	|计算数据的最小值/最大值|
|.idxmin() .idxmax()	|计算数据第一个最大值/最小值所在位置的索引，给索引或切片使用(自定义索引，排除null/NA等空值)|

​    
