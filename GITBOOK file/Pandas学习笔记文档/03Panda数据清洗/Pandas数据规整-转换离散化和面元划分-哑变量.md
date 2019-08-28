Pandas数据规整 - 转换 - 离散化和面元划分
===

---

离散数据和连续数据

* 连续数据：
    * 例如：降雨量：0，5， 10， 100
    * 例如：年龄：0， 10， 20， 50， 100
    * 并不是用数值表示就是连续数据，例如性别用0，1表示，仍然是离散数据
* 离散数据
    * 例如：降雨量：晴，小雨，中雨，大雨，暴雨
    * 例如：年龄：幼年，少年，青年，中年，老年

离散数据和连续数据的区分方式：

    **如果在两个数据之间，可以插入新的数据，数据的应用不会出错，就是连续数据，否则就是离散数据
    或者：数据去重的数量，如果太多，就是连续数据，否则就是离散数据（多的定义不好确定）

----

为什么区分两类数据

不同数据做指标运算时候是不一样的
* 连续数据：可以做运算
    * 例如年龄，可以求平均年龄/最大/最小/年龄标准差。。。
    * 不能做分组基准（因为太多了）只能做聚合运算
* 离散数据：不能做运算，但可以做计数
    * 例如年龄：可以计数每个年龄段有多少人（频次）
    * 可以做分组基准
    

数据分析时，为了方便分析，有时需要将连续数据转为离散数据（离散化，面元划分）

（没有离散数据转连续数据的需求，因为计算出来的指标无意义（如性别计算处1.5））

---

为了便于分析，连续数据常常被离散化或拆分为“面元”（bin，分组区间）

连续数据离散化：降雨量、年龄、身高这类连续数据，要分析：只能画直方图，无法分组聚合
，所以可以将连续数据离散化，例如降雨量转为 小雨中雨大雨暴雨，年龄转为 少年青年中年老年，就可以分组聚合
举例: 一组年龄数据,将它们划分为不同的年龄组:
划分为 [18,25),[26,45),[35,60),[60,~)以上几个面元

```python 
# 年龄
ages = [18, 22, 25, 27, 21, 23, 37, 31, 61, 45, 41, 32]
# 面元区间
bins = [18, 25, 35, 60, 100]
```

使用cut命令进行分割面元

```python 
cats = pd.cut(需要分割的数据,面元区间)
cats =pd.cut(ages,bins)
# ages数据将会按照给定的区间进行分割
type(cats)
>>  pandas.core.arrays.categorical.Categorical
返回的是categories对象（划分的面元），可看做一组表示面元名称的字符串
底层含有：
一个codes属性中的年龄数据标签
一个表示不同分类的类型数组
```

```python
cats.codes # 查看结果所在区间
>> array([0, 0, 0, 1, 0, 0, 2, 1, 3, 2, 2, 1], dtype=int8)
数组中的数字表示对应划分区间的索引,0,表示(18,25],后面依次类推
这里的区间划分都是左开右闭区间.
cats.categories  # 类型，分组区间
```

统计面元计数 

```python 
# pd.cut结果的面元计数
cats.value_counts()  # 统计每个分组区间的数据个数
pd.value_counts(cats)
```

cut方法：默认是左开右闭区间，不包含起始值，包含结束值
right=False后，左闭右开区间，包含起始值，不包含结束值
**修改面元名称**

```python 
cat3 = pd.cut(ages, bins)
cat3 = pd.cut(ages, bins, labels=False)  # 去掉面元名称,只显示切割后的索引值
cat3 = pd.cut(ages, bins, labels=['少年', '青年', '中年', '老年'])  # 自定义面元名称,分割的结果显示为各自所在区间值
cat3.codes  # 返回的仍然是索引所在位置
cat3
```

不指定分割该如何操作

```python 
cat4 = pd.cut(ages, 4, precision=2)  # 将数据分为4组，限定区间浮点数位数为2
cat4 = pd.cut(ages, 4, precision=2,right = False) 
cat4 
#分割原理: 会拿最大值减去最小值除以分割的数量, 如果right 不为Faslse, 由于默认是左开右闭,无法取到最左边的值,所以要将最左边的偏移一个值.
```

cut 为等比例划分,将单元长度分为指定份数,但是每组分割得到的数据是不同的

qcut为等数划分,这种划分使得每组划分得到的结果数量是基本一样多的

```python 
cat5 = pd.qcut(ages, 4) # 按照默认的规则划分为4分,每组数据数量一致
cat5
pd.value.counts(cat5)
>>>>
(38.0, 61.0]       3
(29.0, 38.0]       3
(22.75, 29.0]      3
(17.999, 22.75]    3
 
 pd.value.counts(cat5).sort_values() # 排序
```

```python 
cat6 = pd.qcut(ages, [0, 0.25, 0.5, 0.75, 1])  # 手动输入分割
cat6 = pd.qcut(ages, [0, 0.3, 0.5, 0.8, 1])
cat6
```

---

分位数和桶分析
---

Pandas有一些能根据指定面元或样本分位数将数据拆分成多块的工具（比如cut和qcut）

将这些函数跟groupby结合起来，就能实现对数据集的桶（bucket）或分位数（quantile）分析

### 例：对下面数据集进行数据分析

| gender | age  | grade |
| ------ | ---- | ----- |
| male   | 18   | 50    |
| female | 28   | 90    |
| female | 38   | 88    |
| male   | 48   | 61    |
| 。。。 | ...  | ....  |

```python 
#准备数据 生成100个成绩,性别,年龄的随机数据
age = np.random.normal(25,5,100).astype(np.int)
grade = np.around(np.random.normal(60,10,100), decimals=1)
gender = ['male', 'female'] * 40 + ['male'] * 20

a = pd.DataFrame({'gender': gender, 'age': age, 'grade': grade})
a.head()
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/P3tiuuDk1f2L.png?imageslim)

对数据进行描述性分析

* 出指标：能出多少指标？（十几个）
    * 先1列分析，再2列组合分析，再三列组合分析
    * 对连续值分析：运算（mean(),求平均值）
    * 对离散值分析：计数（size(),频次，个数）
* 可视化

```python 
a.shape # 班级人数
a['grade'].mean() # 平均分
a.loc[:,'age'].mean() # 平均年龄
a.groupby('gender').size()# 男女生人数
a.groupby('gender')['age'].mean() # 男女生平均年龄
a.groupby('gender')['grade'].mean()# 男女生平均分数
a.age.max()
a.grade.min()
a.grade.median()
a.grade.max()
```


```python 
因为数据只有1列离散值（可做分组基准），组合计算后指标比较少，也不能做透视表和交叉表

解决方法：将连续值列离散化后继续分析

```

#### 桶分析（分位数分析）

```python 
bins = [5, 10, 15, 20, 25, 30, 35]  # 年龄分组
a['age_cut'] = pd.cut(a.age, bins) # 将分割的列加到原来的表中
a.head()
```

```python 
b = a.age_cut.value_counts() # 不同年龄段人数汇总
b.plot.bar()
```


```python 
#3列分析
# 统计不同年龄段,不同性别之间个数差异
pd.crosstab(a.age_cut,a.gender)# 使用交叉表进行多列统计
a.pivot_table('grade', index='age_cut', columns='gender',aggfunc =len) # 得到的结果是一样的
```


```python 
# 不同年龄段/不同性别的成绩差异
# a.groupby('age_cut')['grade'].mean() # 差一个维度
a.pivot_table('grade', index='age_cut', columns='gender')
```

举例2 :桶分析


```python 
frame = pd.DataFrame({'data1': np.random.randn(1000), 'data2': np.random.randn(1000)})
frame.head()
```

```python 
q =pd.cut(frame.data1,4) # 第一列数据分为4个数据段
type(q) 
>>>pandas.core.series.Series
type(q.cat) 
>> pandas.core.arrays.categorical.CategoricalAccessor
q.cat  # 需要取cat才能继续操作
q.cat.codes
q.cat.categories

```

```python 
# 由 cut 返回的Categorical 对象可直接传递到groupby,我们可以进一步对data2进行操作.
frame.groupby(q).size()
# frame.groupby(q)['data2'].mean()
frame.groupby(q).sum()
```

----

计算指标/哑变量（了解）
---

一种常用于统计建模或机器学习的转换方式是：将分类变量（categorical variable）转换为 哑变量、指标矩阵（虚拟变量，独热（one-hot）编码变量）

如果DataFrame的某一列含有k个不同的值，则可以派生出一个k列矩阵或DataFrame（其值全为1和0）

pandas有一个get_dummies函数可以实现该功能

#### 独热编码的作用：将不能计算的字符串转为可以计算的数值（表格，或矩阵）

字符串：'一个对统计应用有用的方法：结合get_dummies和如cut之类的离散化函数'

    [统计,应用,有用,方法,结合,离散化,函数]

将人能识别的字符串，转为计算机能计算的数值
* 最简单的方法：例如性别数据，[男, 女]转为[0, 1]
    * 缺点：转后数值是分大小的，原值则不分，不很精确
* 独热编码方法：
    * [1,1,1,1,1,1,1]
    * 列表内有7个词，每个词出现的位置为1， 其他位置为0
    * 统计：[1,0,0,0,0,0,0]
    * 方法：[0,0,0,1,0,0,0]
* 词向量。。。


```python 
# 测试数据
df = pd.DataFrame({'key': ['b', 'b', 'a', 'c', 'a', 'b'], 'data1': range(6)})
```

![1565675053014](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1565675053014.png)

```python 
手动转为独热编码

    [a,b,c]
    [1,1,1]
    
    a: [1,0,0]
    b: [0,1,0]
    c：[0,0,1]
    
    [b,b,a,c,a,b]
    b:[1,1,0,0,0,1]
    a:[0,0,1,0,1,0]
```

```python 
pd.get_dummies(df.key) 
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/BHwB4rz0QNeg.png?imageslim)

合并两个表格

```python 
dummies = pd.get_dummies(df.key, prefix='key') # prefix 代表前缀
dummies
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/KUGyekI64fFk.png?imageslim)

举例: 将一组数据转为哑变量
一个对统计应用有用的方法,结合get_dummies和cut之类的离散化函数

```python
#生成随机数据
values = np.random.rand(10)
# 面元划分
bins = [0,0.2,0.4,0.6,0.8,1]
x = pd.cut(values,bins)
# 转为哑变量 
pd.get_dummies(x)
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190813/Rvc7xqYd3aWg.png?imageslim)