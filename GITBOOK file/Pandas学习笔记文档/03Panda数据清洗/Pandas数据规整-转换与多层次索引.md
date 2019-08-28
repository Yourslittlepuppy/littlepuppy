# Pandas 数据规整 -转换

```python
import numpy as np
import pandas as pd
```

Pandas数据排序
---

.sort_index() 在指定轴上根据索引进行排序，索引排序后内容会跟随排序

```python
b = pd.DataFrame(np.arange(20).reshape(4,5),index=['c','a','d','b'])
b
```

sort_index() 按索引排序


```python
b.sort_index()  # 默认按行索引排序，默认升序
b.sort_index(axis=1, ascending=False)  # 按列索引排序，降序
```

sort_values() 按值排序


```python
dates = pd.date_range('20130101', periods = 10)
dates
df = pd.DataFrame(np.random.randn(10,4), index = dates, columns = ['A','B','C','D'])
df
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190809/BKdLDNiOwOkw.png?imageslim)


```python
# 默认按行排序（这一列的所有行）
df.sort_values(by='A')  # 指定排序基准列
df.sort_values(by='A', ascending=False)  # 倒序
```

```python
# 按列排序（一行的所有列）
df.sort_values(axis=1, by='2013-01-01', ascending=False)
```

关于排序中的缺失值问题

排序不论升序降序，缺失值永远排在最后

```python
c.sort_values(by=0)  # 升序，缺失值在最后
c.sort_values(by=0, ascending=False)  # 降序，缺失值还在最后
```

---


随机排列和随机采样
---


### 随机排列

利用numpy.random.permutation函数可以实现对Series或DataFrame的列的随机排序工作（permuting，随机重排序）

通过需要排列的轴的长度调用permutation，可产生一个表示新顺序的整数数组：


```python
# 随机排列序列
a = [1,2,3,4,5,6,7]
np.random.permutation(a) # 将上面的数组随机打乱返回
```

对dataFrame进行操作

```python
df = pd.DataFrame(np.arangr(5*4).reshape(5,4)))
# 打乱行索引
df.loc(np.random.permutation(df.index))
# 行索引的顺序已经被打乱,但是每行对应的值并没有被改变.对应索引的单元格还是远来的数据
```

![1565610813429](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1565610813429.png)


```python
随机重排行索引和列索引
index = np.random.permutation(df.index)
columns = np.random.permutation(df.columns)
df.loc[index, columns]
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/XFKtnToorfPG.png?imageslim)

**随机采样**

choice(),从一个序列中随机抽取某些值

```python
a = [1,2,3,4,5,6,7]
np.random.choice(a)
np.random.choice(a, size=3)  # 放回抽样,
np.random.choice(a, size=3, replace=False)  # 不放回抽样，不重复抽取 默认replace = True
```


```python
# 按行随机取n个数
df.loc[np.rando.choice(df.index,size =3,replace = Fasle)]
# 按行列随机取样
# 按行、按列随机采样
index = np.random.choice(df.index, size=3, replace=False)
columns = np.random.choice(df.columns, size=2, replace=False)

df.loc[index, columns]
```

重新索引（修改索引）
---

reindex() ，重新索引，创建一个适应新索引的新对象

一种变相的**查询方式**，类似在查询中加入新行新列

* 直接赋值修改索引
* set_index(), reset_index()：普通行列和索引互相转换
* rename是将原索引某些值替换为新值
* reindex则是将整个索引重建（并不替换索引值，而是增减索引或改变顺序，原索引对应的值关系不变）
    * reindex可以理解为一种查询方式
        * loc查询如果索引值不存在，会报警告
        * reindex查询如何索引值不存在，会新增一行或一列新值，值为缺失值


```python
obj = pd.Series([4.5,7,2,-5.3], index=['d','b','a','c'])
```

**直接修改索引:**

* 索引修改后，值没有跟着变化
* 修改值必须和原索引长度保持一致，不能增加或删除索引

```python
obj.index = ['a','b','c','d']# 仅仅只是修改了索引,但对应的值没有改变,不可取
# obj.index = ['a','b','c']  # 长度不一致，报错
obj.rename({'a': 'aaa', 'b': 'bbb', 'x': 'xx'})# 使用rename 对索引值进行修改
返回的是一个新的对象.并没有对原来的表有更改
```

**正确做法:**
使用reindex ()重新索引

```python
obj.reindex(['b', 'd', 'a'])  # 索引个数不一致,舍弃缺失值
obj.reindex(['a','b','c','d','e'])# 比原先多一个则增加,缺失值为NaN
# 新增的缺失值，填充默认值
obj.reindex(['b','d','a','c','e'], fill_value=0)
obj.loc[['a', 'b' ,'c', 'd', 'e']]  # 结果同上，唯一区别是警告，建议用reindex()
```

**注意上面的重命名索引都不是就地修改,都是返回一个新的数据,**

#### DataFrame的索引重建

```python
frame = pd.DataFrame(np.random.randint(30,100,(3,3)), index = ['语文','数学','英语'], columns = ['张三','李四','王五'])
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/XEGG7t0J9FBX.png?imageslim)


```python
# 重建行索引
frame.reindex(['数学','英语','语文'])
frame.reindex(['数学','编程','语文'])
# 重建列索引
frame.reindex(['张三', '赵六', '王五'], axis=1)
frame.reindex(columns=['张三', '赵六', '王五'])
# 重建行列索引
frame.reindex(index=['语文', '编程', '英语'], columns=['张三', '赵六', '王五'])
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/zXAb9OJq1i40.png?imageslim)

原先对应的数据并没有变化,只是位置发生了改变.之前没有定义的索引行或列值则为空.
reindex()类似查询,但查询的可以是没有索引,不会报错

### 带有重复值的轴索引
---

许多Pandas函数要求标签唯一，但这不是强制的

```python
obj = pd.Series(range(5), index=['a','a','b','b','c'])
obj['a']
obj['a'].values# 返回索引为a 的值,类型是array
```

```python
obj.index.is_unique   False
```

## Pandas数据规整 - 转换 - 层次化索引
---

层次化索引（hierarchical indexing）使你能在一个轴上拥有超过1个索引级别，

多层索引可以对数据结构升维，能以低维度形式处理高维度数据

用多层索引 (Multi-index) 的 Series/DataFrame,存储2维/3维或以上维度的信息


```python
data = pd.Series(np.random.randn(9), index=[['a', 'a', 'a', 'b', 'b', 'c', 'c', 'd', 'd'], [1, 2, 3, 1, 3, 1, 2, 2, 3]])
data
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/fPlo2kecAztQ.png?imageslim)

MultiIndex 的对象，里面有 levels 和 labels 二类信息(层次索引的标签和层次索引的值（标签位置)


```python
data.index
```

```python
MultiIndex(levels=[['a', 'b', 'c', 'd'], [1, 2, 3]],
           codes=[[0, 0, 0, 1, 1, 2, 2, 3, 3], [0, 1, 2, 0, 2, 0, 1, 1, 2]])
# 解析:
levels 为 层次化索引 
codes 中是一个索引值,第一个矩阵表示的是第一层索引中的索引对应值,第二个矩阵为第二个矩阵对应的索引值
```

**Series层次化索引的查询**

```python
# 默认索引 索引查找的值都是按照顺序查找的.
data[0]  # 查询单值
data[[0, 3]]  # 查询多值
data[1:4]  # 切片 
# 自定义索引,默认查询外层索引
data['a']  # 查询单值
data[['a', 'c']]  # 查询多值
data['a':'c']  # 切片
# 自定义索引，复杂查询
# loc查询
data.loc['a']  # 查询外层索引
data.loc['a', 2]  # 外层、内层
data.loc[:, 2]  # 外层所有，内层2
```

#### 将层次化索引的Series转为DataFrame

多层索引的 Series 其实和 DataFrame 维度一样，只是展示形式不同

重塑就是通过改变数据表里面的 行索引 和 列索引 来改变展示形式

```python
data.unstack()
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/cvQAG9KI9Obt.png?imageslim)


```python
data.unstack().T# 交换行列索引顺序
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/H933dzjcEJTj.png?imageslim)

 ##  DataFrame层次化索引

### 使用DataFrame的列或行进行索引（重要）[¶](http://localhost:8888/notebooks/3 Pandas数据规整 - 转换 - 层次化索引（了解）.ipynb#使用DataFrame的列或行进行索引（重要）)

使用set_index()将DataFrame的一个或多个列当做行索引来用，或者将行索引变成DataFrame的列

```python 
frame2 = pd.DataFrame(
    {'a': range(7), 'b': range(7, 0, -1),
     'c': ['one', 'one', 'one', 'two', 'two', 'two', 'two'],
     'd': [0, 1, 2, 0, 1, 2, 3]}
)
frame2
```

```python 
# 将普通行转为行索引
frame2.set_index('a') # 将a列转为行索引
frame3 = frame2.set_index(['c','d'])
frame2.set_index(['c', 'd'], append=True,drop = False)  # 增加索引，非替换，保留原索引  
# append = False 则表示删除原来的索引
# drop = False 表示保留作为索引的值
```

行索引转为普通列


```python 
frame3
frame3.reset_index()
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/QHDE3R7x3ah0.png?imageslim)

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/XXFJRjBinfNE.png?imageslim)

通过修改行列索引值实现层次化索引

```python 
symbol = ['BABA', 'JD', 'AAPL', 'MS', 'GS', 'WMT']
data = {'行业': ['电商', '电商', '科技', '金融', '金融', '零售'],
        '价格': [176.92, 25.95, 172.97, 41.79, 196.00, 99.55],
        '交易量': [16175610, 27113291, 18913154, 10132145, 2626634, 8086946],
        '雇员': [101550, 175336, 100000, 60348, 36600, 2200000]}
df2 = pd.DataFrame(data, index=symbol)

df2.name='美股'
df2.index.name = '代号'
df2
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/102yegbEsH6T.png?imageslim)

使用MultiIndex 进行多层次化转换,

```python 
# 正确：使用MultiIndex赋值
df2.index = pd.MultiIndex.from_tuples(
    [('中国公司','BABA'), ('中国公司','JD'), ('美国公司','AAPL'), ('美国公司','MS'), ('美国公司','GS'), ('美国公司','WMT')],
    names=('country', 'company'),
)
df2
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/M270gOCdloNw.png?imageslim)

```python 
# 没有列索引层次化方法，可以转置后操作原列索引
df2x = df2.T
df2x
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/40zqKO6iGhsa.png?imageslim)

### 直接创建多层索引DataFrame（了解）

```python 
frame = pd.DataFrame(
    np.arange(12).reshape((4, 3)),
    index=[['a', 'a', 'b', 'b'], [1, 2, 1, 2]],
    columns=[['Ohio', 'Ohio', 'Colorado'],['Green', 'Red', 'Green']],
)

frame.index.names = ['key1', 'key2']
frame.columns.names = ['state', 'color']

frame
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/69kSKS34PPUW.png?imageslim)


```python 
# 或者这样创建
midx = pd.MultiIndex(
    levels=[['a', 'b'], [1, 2]],
    codes=[[0, 0, 1, 1], [0, 1, 0, 1]],
    names=['key1', 'key2']
)
mcol = pd.MultiIndex(
    levels=[[ 'Colorado', 'Ohio'], ['Green', 'Red']],
    codes=[[1, 1, 0],[0, 1, 0]],
    names=['state', 'color']
)

frame = pd.DataFrame(
    np.arange(12).reshape((4, 3)),
    index=midx,
    columns=mcol,
)

frame
```

### DataFrame层次化索引查询

由外而内,先查外面一层,然后在向内查询


```python 
frame['Ohio']  # 外层列索引
frame['Ohio', 'Green']  # 外层列，内层列
```


```python 
frame.loc['a']  # 外层行索引
frame.loc['a', 2]  # 外层行索引，内层行索引
frame.loc['a', 'Ohio']  # 外层行，外层列
```


```python 
# 外层行，内层行，外层列，内层列
frame.loc['a', 2]['Ohio', 'Red']

#  外层行，外层列，内层行，内层列
frame.loc['a', 'Ohio'].loc[1, 'Red']
```

### 重排与分级排序

调整某条轴上各级别的顺序，或根据指定级别上的值对数据进行排序

```python
frame # 查看原始数据
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/iG7OVsQJUaDH.png?imageslim)

```python 
frame.swaplevel()  # 默认交换行索引

frame.swaplevel('key2', 'key1')  # 手动指定调换索引name
frame.swaplevel('key1', 'key2')  # 交换顺序，效果一样

frame.swaplevel(0, 1)  # 手动指定调换索引层级
frame.swaplevel(1, 0)
# 上面都是对key2和keys1进行了内外层索引交换,但并不关心其写的顺序,变化后的结果是一样的
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/sNfESVKCgYcJ.png?imageslim)

```python
# frame.swaplevel(axis=1)  # 交换列索引
frame.swaplevel('state','color',axis=1)  # 交换列索引
frame.swaplevel('color','state',axis=1)
# frame.swaplevel(0,1,axis =1)
# frame.swaplevel(1,0,axis =1)
# 都是交换内外层列索引.同样写入参数的顺序对结果没有影响.
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190812/CsyjoITGQ3N4.png?imageslim)

 

```
### 根据级别汇总统计

许多对DataFrame和Series的描述和汇总统计函数都有一个level选项，它用于指定在某条轴上计算的级别

其实是利用了pandas的groupby功能
```

### 根据级别汇总统计

许多对DataFrame和Series的描述和汇总统计函数都有一个level选项，它用于指定在某条轴上计算的级别

其实是利用了pandas的groupby功能

```python
frame.sum() # 安行求和 
>> 
state     color
Ohio      Green    18
          Red      22
Colorado  Green    26
######################
frame.sum(level = 'key1') # 以key1索引分组求和
frame.groupby('key1').sum()
frame.groupby(level= 'key1').sum()
frame.groupby(['a', 'a', 'b', 'b']).sum()  # 手动构造分组基准
```

![1565662050162](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1565662050162.png)

```python 
# 以内层行索引为分组基准
frame.groupby('key2').sum()  # 分组基准，行索引name
frame.groupby(level='key2').sum()  # level传入分组基准
frame.groupby([1,2,1,2]).sum()  # 手动构造分组基准
```

案列求和

```python
frame.sum(axis=1)
frame.sum(axis = 1,level = 'color')
frame.groupby((axis =1,level='color'))
frame.groupby(['Green','Red','Green'],axis =1)
frame.sum(axis=1, level='state')  # Ohio下的两列相加
              
```

```python 
frame.groupby(level='state', axis=1).sum().sort_index(ascending=False, axis=1)

# 和上面的区别：使用手打的分组基准代替level定义
# 报错，直接传入分组索引值，默认使用最里层列索引
# frame.groupby(['Ohio', 'Ohio', 'Colorado'], axis=1).sum().sort_index(ascending=False, axis=1)

# 列索引交换层级
frame.swaplevel(axis=1)
frame.swaplevel(axis=1).groupby(['Ohio', 'Ohio', 'Colorado'], axis=1).sum().sort_index(ascending=False, axis=1)
```

