

![插图1](https://github.com/Yourslittlepuppy/littlepuppy/blob/master/pictures/%E5%9B%BE%E7%89%87.jpg?raw=true)


# `random`库概述
使用random库主要目的是生成随机数。

例：  
`random.randint(a,b)` --->生成[a,b]的整数（闭区间）

`random.randrange(a,b)`----->生成[a,b]的数（左闭又开）

> ## random库的常用函数
  
### **random库与随机数运用**
|函数|描述|
|:----|:----|
|seed(a=None) |初始化随机数种子，默认值为当前系统时间
random() |生成一个[0.0, 1.0)之间的随机小数
randint(a, b)| 生成一个[a,b]之间的整数
getrandbits(k)| 生成一个k比特长度的随机整数
randrange(start, stop[, step]) |生成一个[start, stop)之间以step为步数的随机整数
uniform(a, b)| 生成一个[a, b]之间的随机小数
choice(seq) |从序列类型(例如：列表)中随机返回一个元素
shuffle(seq)| 将序列类型中元素随机排列，返回打乱后的序列
sample(pop, k) |从pop类型中随机选取k个元素，以列表类型返回
#### 实例举证：
**`shuffle`**  
就地修改列表，随机排列    
```python 
ls = [1,3,4,5,8,10]
random.shuffle(ls)
print(ls)
>>[3, 1, 5, 10, 4, 8]  
```
**sample:**    
```python
random.sample(ls,2)
>>[5,8]  
random.sample([1,1],2)  
>>[1,1]   
```  

*随机取数，但不取重复的两个数*  
*[1,1 ]虽然数字相同，但是索引不一样，所以不是相同两个数* 






>## 元组  

### **定义：**
元组（tuple）是关系数据库中的基本概念，关系是一张表，表中的每行（即数据库中的每条记录）就是一个元组，每列就是一个属性。  
元组类型**一旦定义就不能修改**，在编程中不够灵活，同时，元组类型的所有操作都可以由列表类型实现。因此，如果需要自定义变量，通常以列表类型代替元组类型使用。如果确认编程中不需要修改数据，可以使用元组类型。
>>**元组的常用操作：**  

一个元组的写法：  
t1=(1,)  ---->加逗号  
```python
ls = [1,2,3,4]
t2=tuple(ls)
>>(1,2,3,4)
```
元组一旦创立变不可更改，但是创建的元组内含有引用地址的则可以修改引用地址内的数据，如：
```python
t3 = [1,[2,3,4],'a',None]  
t3[1][0]= 5  
>> [1,[5,3,4]],'a',None] 
```
**引用类型,注意元组引用地址**  
**namedtuple**     
对于namedtuple，你不必再通过索引值进行访问，你可以把它看做一个字典通过名字进行访问，只不过其中的值是不能改变的。   
例： 
```python
from collections import namedtuple  
Point= nametuple('Point',['x','y'])  
type(Point)  
>> type ---->  #Point 是一个类型
p1=Point(4,5)  
print(p1)  
>>Point(x=4, y=5)  
Point(x=20,y=10)  
p1.x  p2.y  
>>(20,10)

Student = namedtuple('S','name,age')  
Student  
>>'__main__.s'   #为标识符，名字为S ,名字给人看的，程序员使用标识符  
tom = Student('tom',20)    
tom  
>>S(neme='tom',age=20)  
jerry = Student('Jerry',18)   
jerry 
>> S(name='Jerry',agr = 18) 
```

>## 字符串  
### **定义：**  
字符串是字符的序列表示，根据字符串的内容多
少分为单行字符串和多行字符串。  
单行字符串可以由一对单引号（'）或双引号（"）
作为边界来表示，单引号和双引号作用相同。  
多行字符串可以由一对三单引号（'''）或三双引
号（"""）作为边界来表示，两者作用相同。  
>>**字符串的常用操作：**  

#### 字符串操作符 
操作符|描述
|:----|:-----|
x + y| 连接两个字符串x与y
x * n 或 n * x| 复制n次字符串x
x in s |如果x是s的子串，返回True，否则返回False
 #### 字符串处理函数   

  函数|描述
  |:----|:-----|
len(x) |返回字符串x的长度，也可返回其他组合数据类型的元素个数
str(x) |返回任意类型x所对应的字符串形式
chr(x)| 返回Unicode编码x对应的单字符
ord(x)| 返回单字符x表示的Unicode编码
hex(x) |返回整数x对应十六进制数的小写形式字符串
oct(x)| 返回整数x对应八进制数的小写形式字符串  
 #### 字符串处理方法
方法|描述
 |:----|:-----|
str.lower() |返回字符串str的副本，全部字符小写
str.upper() |返回字符串str的副本，全部字符大写
str.split(sep=None) |返回一个列表，由str根据sep被分割的部分构成
str.count(sub) |返回sub子串出现的次数
str.replace(old, new) |返回字符串str的副本，所有old子串被替换为new
str.center(width, fillchar) |字符串居中函数，fillchar参数可选
str.strip(chars)| 从字符串str中去掉在其左侧和右侧chars中列出的字符
str.join(iter) |将iter变量的每一个元素后增加一个str字符串  


元组常用命令举例： 
```python
x =4  
y=10  
s1=f"{x}-->{y}
s1   
>>'4-->10'  
s2=f'{P1}’ # 
>>'Point(x=4,y=5)'  

":".join(map(str,range(9)))  --->map（str,abc)  map将abc转为字符串格式
>>'0:1:2:3:4:5:6:7:8'  
"*".join('abcd')  
>>'a\*b\*c\*d'

"*".join(['abcdef'])  
>>'abcdef'  
s1='a,b,c,d,e'
**s1.split(',')**  
>>['a', 'b', 'c', 'd', 'e']  --> #以","进行分割，立即返回一个列表
s.split(maxsplit=2) -->#默认分割几次，默认以最大空格进行切  
s.rsplit() #--->反向切片  
s.splitlines()#--->安照换行符进行分割  
s.partition(',') #--->切一次，立即返回一个三元组 s.replace(old,new[,count])-->  替代


s3='a,b,c,d,e'  
**s3.parttition(',')** --->  ('a',',','b,c,d,e') 常用于切头去尾。  
**s.tittle()**   #英文首字母大写 
s.capitalize()  #英文第一个字母大写  
**s.center(30,'*")**  --->  居中填充  
s='      I am very very very very sorry   '
s.strip("a") -->  #去掉两头的"a"字符
**s.stirp()**  
>> I am very very very very sorry 

s.strip(' \t\nmai)   # --->去掉后面出现的字符，直到第一个非出现字符
>>'very very very sorry'  
**s.find()**#---->找到返回正索引，找不到怎返回-1
s.startswith('',0,-3)  #--->可加区间的前缀和后缀  
s.endswith() 
``` 


>字符串格式化：  

---- C  风格 
"I am %04d"%20   
\>> I am 0020   
 "I am %-04d"%20    
 \>> I am 20  (此处有两个空格)   
 format函数  ---python常用  

 **format()方法的格式控制：**  
 |：|<填        充>|<对     齐>|<宽  度>|<,>|<.精度>|类型|  
 |:--|:--|:--|:--|:--|:--|:--|
 引导符号|用于填充的    单个字符|<左对齐>   右对齐 ^ 居中|槽的设定输入宽度|数字的千位分隔符适用于整数和浮点数|浮点数小数部分的精度或字符串的最大输出长度|整数类型b,c,d,o,x,X 浮点数类型  

 ```python
 octets =[192,168,160,1]  
 print("{:02x}{:02x}{:02x}{:02x}".format(*octets))
 >>c0a8a001             #参数解构
 输出IP地址的16进制格式
   

```

