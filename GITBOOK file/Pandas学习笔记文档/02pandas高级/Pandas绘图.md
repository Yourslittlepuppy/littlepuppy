Pandas绘图
===

---

Pandas绘图是基于Matplotlib的上层封装

正常工作一般先用Pandas绘图，再辅以Matplotlib修改组件

---

Pandas绘图的优势：

* 代码简洁
* 针对Pandas数据结构专门优化过（Series/DataFrame）

劣势：

* Pandas绘图自定义程度较差
* matplotlib自定义程度高

```python 
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
```

```python
# plt.style.use('seaborn')  # 改变图像风格
plt.rcParams['font.family'] = ['Arial Unicode MS', 'Microsoft Yahei', 'SimHei', 'sans-serif']  # 解决中文乱码
# plt.rcParams['axes.unicode_minus'] = False  # simhei黑体字 负号乱码 解决
```

实战演练

```python
ts = pd.Series(np.random.randn(1000), index=pd.date_range('1/1/2000', periods = 1000))
ts = ts.cumsum()
ts.head()
```

Series绘图
---

matplotlib绘图

```python
plt.figure(figsize=(18, 8))  # 图像大小
plt.title('stock:股票价格')  # 标题
plt.plot(ts.index, ts)  # 绘图 # 横坐标与纵坐标
```

Pandas绘图
Pandas绘图和Matplotlib绘图代码可以结合使用


```python
# 方法一: 结合绘图
plt.figure(figsize=(18, 8))  # 图像大小
ts.plot()
plt.title('stock:股票价格')  # 标题
# 方法二: 直接使用pandas绘图
# 参数写入函数内部
ts.plot(figsize=(18, 8), title='stock:股票价格')
```

## DataFrame 绘图

```python
df = pd.DataFrame(
    np.random.randn(10, 4).cumsum(0),
    columns=['A', 'B', 'C', 'D'],
    index=np.arange(0, 100, 10)
)
df
```

**使用Matplotlib绘图:**

```python
# plt.plot(df.index, df.A)
# plt.plot(df.index, df.B)
# plt.plot(df.index, df.C)
# plt.plot(df.index, df.D)
```

**Pandas 绘图**

```python
df.plot()
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/MIrkP1etknlj.png?imageslim)

### Pandas绘图的plot方法的常见参数

核密度估计图比直方图的优势
* 线条表现数据分布，可以对比多组数据的分布情况
* 分布情况平滑，更容易观看

```python
df.plot()  # 折线图
df.plot(kind='bar')  # 柱状图
df.plot(kind='hist', alpha=0.5)  # 直方图 
df.plot(kind='kde', figsize=(18, 5), alpha=0.5, grid=True, legend=True)  # 核密度估计  # 透明度为0.5  是否需要开启网格
# Matplotlib修饰图像，修改网格粗细和透明度
plt.grid(linewidth=0.2, alpha=0.5)
```

## 柱状图

一些常见图表有专有绘图函数，如柱状图
plot.bar()和plot.barh()分别绘制水平和垂直的柱状图
Series和DataFrame的索引将会被用作X（bar）或Y（barh）刻度

```python
data = pd.Series(np.random.rand(16), index=list('abcdefghijklmnop'))
data
```


```python
# 通用函数
data.plot()  # 折线图
data.plot(kind='bar')  # 柱状图
```

![1565264629207](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1565264629207.png)

### Matplotlib子图和Pandas绘图结合

```python
fig, axis = plt.subplots(2, 1)  # Matplotlib操作：绘制2行1列，2个子图
# 可以直接用data.plot.bar 或者当参数一样传入到实例调用中
data.plot(kind = 'bar',ax=axis[0], alpha=0.5)
data.plot.line(ax=axis[1], color='r', alpha=0.5) 
#data.plot.line(ax=axis[1], color='r', alpha=0.5)#默认的绘图是折线图,因此不写就是绘制折线图 
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/vLYu1X18ciQP.png?imageslim)

### 堆积柱状图

```python
df = pd.DataFrame(
    np.random.rand(6, 4),
    index=['one', 'two', 'three', 'four', 'five', 'six'],
    columns=pd.Index(['A', 'B', 'C', 'D'], name='Genus')
)
df
```


```python
# 普通柱状图
# df.plot(kind= 'bar')
df.plot.bar() # 推荐使用
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/ycUWqGi4cEh7.png?imageslim)

```python
# 堆积柱状图
df.plot.bar(stacked=True, alpha=0.5, legend=False)
# 堆积为True, 透明度为0.5, 图例为False
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/EwB4DptCbQSP.png?imageslim)

柱状图的横倒绘制

```python
s = pd.Series([1,1,2,3,4,4,4,4,5,6,6])
```

```python
fig, axis = plt.subplots(2, 1) 
s.value_counts().plot.bar(ax=axis[0])
s.value_counts().plot.barh(ax=axis[1])
```

![mark](http://qiniu.lizhaoxi.cn/blog/20190808/UUzv6IMD0OJ5.png?imageslim)

