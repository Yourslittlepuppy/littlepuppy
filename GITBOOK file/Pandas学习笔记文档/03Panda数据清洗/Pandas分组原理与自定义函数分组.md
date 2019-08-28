
Pandas分组原理
===

---



```python
import numpy as np
import pandas as pd
```


```python
df = pd.DataFrame({
    'name': ['张三','李四','王五','李四','王五','王五','赵六'],
    'chinese': [18, 53, 67, 63, 39, 70, 94],
    'math': [82, 63, 41, 59, 46, 39, 58],
    'english': [68, 52, 90, 86, 60, 98, 64],
    'test': ['一','一','一','二','二','三','一']
})

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>张三</td>
      <td>18</td>
      <td>82</td>
      <td>68</td>
      <td>一</td>
    </tr>
    <tr>
      <th>1</th>
      <td>李四</td>
      <td>53</td>
      <td>63</td>
      <td>52</td>
      <td>一</td>
    </tr>
    <tr>
      <th>2</th>
      <td>王五</td>
      <td>67</td>
      <td>41</td>
      <td>90</td>
      <td>一</td>
    </tr>
    <tr>
      <th>3</th>
      <td>李四</td>
      <td>63</td>
      <td>59</td>
      <td>86</td>
      <td>二</td>
    </tr>
    <tr>
      <th>4</th>
      <td>王五</td>
      <td>39</td>
      <td>46</td>
      <td>60</td>
      <td>二</td>
    </tr>
    <tr>
      <th>5</th>
      <td>王五</td>
      <td>70</td>
      <td>39</td>
      <td>98</td>
      <td>三</td>
    </tr>
    <tr>
      <th>6</th>
      <td>赵六</td>
      <td>94</td>
      <td>58</td>
      <td>64</td>
      <td>一</td>
    </tr>
  </tbody>
</table>
</div>



---

分组的内部结构
---

通过查看分组对象的内部结构，了解其原理


```python
x4 = df.groupby('name')
x4
```




    <pandas.core.groupby.generic.DataFrameGroupBy object at 0x0000022055810DD8>



分组对象常见属性和方法

* ngroups: 组的个数 (int)
* size(): 每组元素的个数 (Series)
* groups: 每组元素在原 DataFrame 中的索引信息 (dict)
* get_group(label): 标签 label 对应的数据 (DataFrame)


```python
x4.ngroups
x4.size()
x4.groups
x4.get_group('李四')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>李四</td>
      <td>53</td>
      <td>63</td>
      <td>52</td>
      <td>一</td>
    </tr>
    <tr>
      <th>3</th>
      <td>李四</td>
      <td>63</td>
      <td>59</td>
      <td>86</td>
      <td>二</td>
    </tr>
  </tbody>
</table>
</div>



分组不能直接输出，通过转为列表、字典或遍历查看分组内部结构

#### 分组转为列表或字典

转为列表


```python
x5 = list(x4)
x5
```




    [('张三',   name  chinese  math  english test
      0   张三       18    82       68    一),
     ('李四',   name  chinese  math  english test
      1   李四       53    63       52    一
      3   李四       63    59       86    二),
     ('王五',   name  chinese  math  english test
      2   王五       67    41       90    一
      4   王五       39    46       60    二
      5   王五       70    39       98    三),
     ('赵六',   name  chinese  math  english test
      6   赵六       94    58       64    一)]




```python
x5[0]
x5[0][0]
x5[0][1]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>张三</td>
      <td>18</td>
      <td>82</td>
      <td>68</td>
      <td>一</td>
    </tr>
  </tbody>
</table>
</div>




```python
x5[1]
x5[1][0]
x5[1][1]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>李四</td>
      <td>53</td>
      <td>63</td>
      <td>52</td>
      <td>一</td>
    </tr>
    <tr>
      <th>3</th>
      <td>李四</td>
      <td>63</td>
      <td>59</td>
      <td>86</td>
      <td>二</td>
    </tr>
  </tbody>
</table>
</div>




```python
x5[2]
x5[2][0]
x5[2][1]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>王五</td>
      <td>67</td>
      <td>41</td>
      <td>90</td>
      <td>一</td>
    </tr>
    <tr>
      <th>4</th>
      <td>王五</td>
      <td>39</td>
      <td>46</td>
      <td>60</td>
      <td>二</td>
    </tr>
    <tr>
      <th>5</th>
      <td>王五</td>
      <td>70</td>
      <td>39</td>
      <td>98</td>
      <td>三</td>
    </tr>
  </tbody>
</table>
</div>




```python
x5[3]
x5[3][0]
x5[3][1]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>赵六</td>
      <td>94</td>
      <td>58</td>
      <td>64</td>
      <td>一</td>
    </tr>
  </tbody>
</table>
</div>




```python
type(x5[3][1])
```




    pandas.core.frame.DataFrame



转为字典


```python
x6 = dict(list(x4))
x6
```




    {'张三':   name  chinese  math  english test
     0   张三       18    82       68    一, '李四':   name  chinese  math  english test
     1   李四       53    63       52    一
     3   李四       63    59       86    二, '王五':   name  chinese  math  english test
     2   王五       67    41       90    一
     4   王五       39    46       60    二
     5   王五       70    39       98    三, '赵六':   name  chinese  math  english test
     6   赵六       94    58       64    一}




```python
x6['李四']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>李四</td>
      <td>53</td>
      <td>63</td>
      <td>52</td>
      <td>一</td>
    </tr>
    <tr>
      <th>3</th>
      <td>李四</td>
      <td>63</td>
      <td>59</td>
      <td>86</td>
      <td>二</td>
    </tr>
  </tbody>
</table>
</div>




```python
x6['王五']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>王五</td>
      <td>67</td>
      <td>41</td>
      <td>90</td>
      <td>一</td>
    </tr>
    <tr>
      <th>4</th>
      <td>王五</td>
      <td>39</td>
      <td>46</td>
      <td>60</td>
      <td>二</td>
    </tr>
    <tr>
      <th>5</th>
      <td>王五</td>
      <td>70</td>
      <td>39</td>
      <td>98</td>
      <td>三</td>
    </tr>
  </tbody>
</table>
</div>



#### 遍历分组内部结构


```python
# 单列分组基准遍历

for method, group in x4:
#     print(method)  # 分组基准
#     print(group) # 分组后的DataFrame
#     print(type(group))
    x7 = group
    
x7
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>赵六</td>
      <td>94</td>
      <td>58</td>
      <td>64</td>
      <td>一</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 多列分组遍历内部结构

for (k1, k2),group in df.groupby(['name', 'test']):
#     print(k1)  # 分组基准1
#     print(k2)  # 分组基准2
#     print(group)  # 分组后的DataFrame
    x8 = group
    
x8
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>chinese</th>
      <th>math</th>
      <th>english</th>
      <th>test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>赵六</td>
      <td>94</td>
      <td>58</td>
      <td>64</td>
      <td>一</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
