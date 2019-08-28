# Pandas数据规整 - 合并

---
| 方法          | 操作                                                         | 含义                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| append        | a.append(b)                                                  | b加到a后面                                                   |
|               | a.append(b,ignore_index=True)                                | Series加到DataFrame后面                                      |
| connect       | pd.connect([a,b,c],axis = 1,sort=False)                      | 将a,b,c三个表按列进行合并,默认不排序                         |
|               | pd.connect([s1,s3],axis = 1,join = 'outer')                  | outer :并集合并<br />innet: 交集合并                         |
| merger        | pd.merge(a,b)                                                | a,b按照相同的列名进行合并,<br />两表长度不一致时不报错,      |
|               | pd.merge(df1, df6, left_on='name', right_on='username').drop('username',axis = 1) | 没有相同表名时使用left_on,right_on 指定参考列进行两表合并<br />还可以删除不需要的合并列 |
|               | pd.merge(a,b,how = 'innner')<br />how方法: inner ,outer ,left,right | 交集,并集,左表为基准,右表为基准                              |
|               | pd.merge(a,b,on = 'name',suffixes=['_L', '_R')               | 两个表的列索引名一致,指定按什么名进行合并,会返回一个加后缀的新表,也可以手动指定后缀名 |
| join          | a.join(b)                                                    | join会按照索引相同进行合并                                   |
| combine_first | a.combine_first(b)                                           | a中的缺失值由b填充,在用b的多余相与a合并                      |


数据合并

Pandas提供了大量方法，能轻松的对Series，DataFrame执行合并操作

* 按行合并
    * 追加：append()
    * 连接：concat()  # 行列均可
* 按列合并
    * 复杂合并：merge()
        * 按行索引合并：join()
* 合并重叠数据(一个表为主，先填充再合并)：combine_first()

```python
import numpy as np
import pandas as pd
```

追加append()
一个表追加到另一个表后面

```python
s = pd.Series([0, 1], index=['a', 'c'])
s1 = pd.Series([2, 3, 4], index=['b', 'd', 'e'])
s.append(s1) # 将s1 添加到s的后面,但是并不会去重
```


```python
# dataFrame 数组操作
df = pd.DataFrame(np.random.randn(8, 4), columns = ['A','B','C','D'])
df
s = df.loc[[3,5]]
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/l136g9e2DGYB.png?imageslim)


```python
df.append(s)# 会在尾部追加
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/Xylcb1evoc8r.png?imageslim)


```python
s2 = pd.Series([1,2,3,4], index = ['A','B','C','D'])
df.append(s2,ignore_index = True)  #Series也可以插入到dataframe中.
这里的ignore_index 表示忽略被加入数据的原先索引.不忽略则继续使用,忽略后会在原基础上自增
```

###  追加合并方法二:
连接connect 


```python
s1 = pd.Series([0, 1], index=['a', 'c'])
s2 = pd.Series([2, 3, 4], index=['b', 'd', 'e'])
s3 = pd.Series([5, 6], index=['f', 'g'])
```


```python
serise 连接
pd.connect([s1,s2,s3])
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/Ub5BPLPHetGb.png?imageslim)

```python
pd.concat([s1, s2, s3], axis=1, sort=False)  # sort参数，控制是否排序
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/ybD4HVowAoX6.png?imageslim)


```python
# 将 s1 和 s3 沿 0轴 连接创建 s4，这样 s4 和 s1 的 index 是有重复的。
s4 = pd.concat([s1, s3])
s4 >>
a    0
c    1
f    5
g    6

```

```python
# 将 s1 和 s4 沿 1轴 内连接 (即只连接它们共有 index 对应的值)
pd.concat([s1, s4], axis=1, join='outer', sort=False)  # 默认outer
# pd.concat([s1, s4], axis=1, sort=False, join='inner')
# outer 连接表示直接将两个数据进行连接,inner 表示连接相同部分
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/JFyihaHf4bCg.png?imageslim)

复杂合并 .merge()和.join()
---

按列合并

merge()函数用于复杂综合数据合并,操作复杂，功能强大

join()是merge()的一个特殊用法，用于按索引合并，操作简单，功能单一


```python
# df1，姓名和分组
df1 = pd.DataFrame({
    'name': ['张三', '李四', '王五', '赵六'],
    'group': ['DBA', 'PM','PM', 'HR']
})
df1
# df2，姓名和入职时间
df2 = pd.DataFrame({
    'name': ['李四', '赵六', '张三', '王五'],
    'date': [2004, 2008, 2012, 2014]
})
df2
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/b5UTsbp2iXVB.png?imageslim)

两个表必须有相关性，才有合并的需要
以两个表相关的列（或行索引）为基准，进行合并

以两个表相关的列（或行索引）为基准，合并

```python
# 方法一:使用查询和添加到列进行合并
# 备份数据
df1 = df.set_index('name').copy()
df2 = df.set_index('name').copy() # 原先的索引没有意思要重新设置
# 将df1表的组队数据加到df2中,这种添加前提是索引必须一致
df2['group']= df1['group']
df2.reset_index() # 加上索引
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/vUuIJEtqd4c1.png?imageslim)

```python
# 除了上面手动合并两个表,有么有简单的方法
# 合并两个对象,默认匹配相同的列名,自动合并
df3 = pd.merge(df1,df2) 
# 如果有不匹配的行则将忽略,不进行保留	
# 要合并的样本量（行数）不同时，合并后的数据会自动扩展，不损失信息#
```


```python
# df4，每个分组的领导，行数少,现在多一表,是领导的表
df4 = pd.DataFrame({
    'group': ['DBA', 'PM', 'HR'],
    'leader': ['钱大', '孙二', '周三']
})

```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/M9lkRI6eVdHV.png?imageslim)

```python
# 现在要想给df3 进行合并,给df3 加上领导该怎么操作
pd.merge(df3,df4)
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/cMttCjof1cc1.png?imageslim)

```python
df5 = pd.DataFrame({
    'group': ['DBA', 'DBA','PM', 'PM', 'HR', 'HR'],
    'skills': ['Linux', '数据库', 'Axuer RP', '社交','招聘', '组织']
})
df5
```

```python
pd.merge(df1, df5)# 会自动在df1的表基础上继续增加
```
![mark](http://qiniu.lizhaoxi.cn/blog/20190813/Sejf7z8YUobL.png?imageslim)

**两个表没有同名同列时,该如何合并?**
即: 没有相同的索引列名该怎么把两个表合并在一起

两个对象没有同名列时，用left_on和right_on强制指定列名对应合并

```python
# df1，姓名 name 和分组
# df6，姓名2 username 和薪资
df6 = pd.DataFrame({
    'username': ['王五', '张三', '赵六', '李四'],
    'salary': [10000, 160000, 7000, 120000]
})
df6
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/h1NRMfv4THgo.png?imageslim)

```python
pd.merge(df1, df6, left_on='name', right_on='username')
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/r4Ww8rOr1XEQ.png?imageslim)

```python
pd.merge(df1, df6, left_on='name', right_on='username').drop('username', axis=1)  # 删除重复列
```

按照行索引合并
---

当要合并数据的行索引相关时，指定 merge() 函数的参数 left_index 与 right_index 的值为 True，就可以实现自动依照索引序号合并

join()函数也能实现，写法更简单

merge()的优势在于更灵活，尤其是当数据集索引值差别很大，数据合并又必须以其中一组数据的索引值为依据时

```python
# df1a，将df1的name列设为行索引
df1a = df1.set_index('name')
# df2a，将df2的name列设为行索引
df2a = df2.set_index('name')

```

按索引合并，最简单的方式 ：join()


```python
df1a.join(df2a)
df1a.join(df2a).reset_index()
pd.merge(df1a, df2a, left_index=True, right_index=True)  # merge实现，同上
```

两个对应列不完全重复的数据集的合并

参数 how

* `how='inner'`，交集，两个表共有的行
* `how='outer'`，并集，两个表所有的行
* `how='left'`，表1的行
* `how='right'`，表2的行


```python
# df7，姓名和时间，姓名列不完全一致
df7 = pd.DataFrame({'name':['张一', '李二', '赵六'], 'data':[2000,2001,2002]})

```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/S9Jj3DVXvJke.png?imageslim)

```python
pd.merge(df1, df7) # 默认按照交集
pd.merge(df1, df7, how='inner')  # 默认，交集
```


```python
pd.merge(df1, df7, how='outer')  # 并集
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/4INCgXfIftpf.png?imageslim)


```python
pd.merge(df1, df7, how='left')  # 以左表为基准
pd.merge(df1, df7, how='right')  # 以右表为基准
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/TF3S8lLyQQMM.png?imageslim)

合并数据集中包含两个或以上相同列名时
---

参数 on 指定用于合并的主键

合并后的数据集中，之前相同的列名会被默认加上 _x 等后缀用于区分

参数 suffixes 可以自定义后缀

```python
# df8，相同的姓名和分组
df8 = pd.DataFrame({
    'name': ['张三', '王五', '赵六', '李四'],
    'group': ['code', 'VP','VP', 'code']
})
df8
```

```python
 pd.merge(df1, df8, on='name')
    
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/f66m7rjAG4QY.png?imageslim)

```python
# 通过设置参数 suffixes 自定义后缀
pd.merge(df1, df8, on='name', suffixes=['_L', '_R'])
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/1YiJT1ACG9vK.png?imageslim)

合并重叠数据（了解）
---

有一类数据组合问题不能用简单的合并（merge）或连接（concatenation(concat)）运算来处理。 如合并全部或部分重叠的两个数据集

举例，我们使用NumPy的where函数，它表示一种等价于面向数组的if-else

#### 以a为基准合并，a的缺失值使用b填充

先给a打补丁（用b填充a的缺失值，再合并）

```python 
a = pd.Series([np.nan, 2.5, np.nan, 3.5, 4.5, np.nan], index=['f', 'e', 'd', 'c', 'b', 'a'])
b = pd.Series(np.arange(len(a), dtype=np.float64), index=['f', 'e', 'd', 'c', 'b', 'a'])
b[-1] = np.nan
```

```python 
ax = a.copy()
ax.isnull()
ax[ax.isnull()]  # 查询a的缺失值
ax[ax.isnull()] = b  # a的缺失值用b填充（操作默认是索引对齐）

```

Series有一个combine_first方法，实现的也是类似功能，

除了用b填充a的缺失值，还带有pandas数据对齐的合并功能

```python 
a2 =a[2:].copy()
b2 = b[:-2].copy()
# 方法一 : 使用原生方式填充缺失值
a2[a2.isnull()]=b2
>>
d    2.0
c    3.5
b    4.5
a    NaN
```

```python
# 2：合并数据
a22 = a2.append(b2)
a22
```

```python
# 3：去重，排序
a22.index.duplicated()
a22[~(a22.index.duplicated())].sort_index()
```

方法2：使用combine_first方法，先打补丁，再合并，再去重排序

```python 
a3.combine_first(b2)
>> 
a    NaN
b    4.5
c    3.5
d    2.0
e    1.0
f    0.0
```

对于DataFrame，combine_first会在列上应用同样操作，可以将其看做：
用传递对象中的数据为调用对象的缺失数据“打补丁”

```python
df11 = pd.DataFrame({'a': [1., np.nan, 5., np.nan], 'b': [np.nan, 2., np.nan, 6.], 'c': range(2, 18, 4)})
df21 = pd.DataFrame({'a': [5., 4., np.nan, 3., 7.], 'b': [np.nan, 3., 4., 6., 8.]})
```

```python
newf11 =df11.combine_first(df21)  # 以df11为基准，先填充缺失值（用df21的值填充df11），再合并(df21的多余行列合并到df11上)
newf21 =df21.combine_first(df11)  # 以df21为基准，先填充（用df11的值填充df21），再合并
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/3D9o7TVUffsz.png?imageslim)

### 总结:


