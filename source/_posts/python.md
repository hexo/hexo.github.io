---
title: python数据分析之pandas
date: 2017-04-27 19:22:11
tags: python
categories: python
---
最近终于开始做一些研究性的工作，学习使用python进行数据分析。首先接触到的就是pandas，现在把关于pandas相关的一些理解记录下来。

### Series
series类似一维数组，他由一组数据及数据对应的标签组成。说是类似数组，但其实和数组是有区别的，主要是由于Series的索引的存在。

```apple js
from pandas import Series, DataFrame
import pandas as pd
```
```apple js
obj = Series([1,2,3,4])# 创建一个Series对象，如果不声明索引，则默认的索引是0到N-1(N是数据的长度)
```
```
obj.index 代表Series对象的索引，我们可以手动声明索引
obj.values 代表Series对象的值，此时才相当于数组，即为python中的list
```
我们可以手动声明series的索引
```apple js
obj2= Series([1,2,3,4], index=['a','b','c','d'])
obj2['c']#通过索引获取元素的值
```
判断是否缺失数据
```apple js
obj2.isnull() #使用series对象的方法，返回series，值为True或False
pd.isnull(obj2) # 使用pandas的方法
pd.notnull(obj2)
```
series对象本身及其索引都有一个name属性。
```apple js
sdata = {'ohino': 3500, 'texas': 7100, 'utah': 500}
obj = Series(sdata)
obj.name = 'population'
obj.index.name = 'state'
```
### Series总结
Series可以看成是一个定长的有序字典，可以认为字典的key值即为对应的Series的值的索引。

### DataFrame
Dataframe是一种表格型的数据结构，可以看成Series组成的字典，最常用的构建方法是传入一个由登场列表或者numpy数组组成的字典。DataFrame会自动加上索引。

```apple js
data = {‘state’: ['ohio','ohio','ohio'], 'year': [2001, 2002, 2003], 'pop': [1.5, 1.6, 1.7]}
frame = DataFrame(data, columns=['year', 'state','pop'])
```
获取元素
```apple js
frame['state']#获取Series，返回的Series与原来的DataFrame有相同的索引。而且其name属性已经被设置为该columns的值，即为state
frame.state # 也可以使用属性的方式获取series
```
当需要获取一行元素时，使用位置或者名称的方式进行获取。
```apple js
frame.ix[2] #获取第三行数据，此时仍为series，只是columns的值变成了索引
```

另一种常见的数据形式是嵌套字典，外层的字典的键作为列，内层字典的键作为行索引，内层字典的键会被合并，排序，形成最终的索引，也可以指定索引
```apple js
frame.values #以二维数组的方式返回DataFrame中的数据
frame.index.name #设置索引的名字
frame.columns.name #设置columns的名字
```
### DataFrame切片
```apple js
obj[val]# 选取DataFrame的单个列或者一组列
obj.ix[val] # 选取DataFrame的单个行或者一组行
obj.ix[:,val] #选取单个列或者列子集
obj.ix[val1, val2] #同时选取行和列
```

### DataFrame和Series之间的运算
默认情况下，DataFrame和Series之间的算术运算会将Series的索引匹配到DataFrame的列，然后沿着行一直向下传播

```apple js
frame = DataFrame(np.arange(12.).reshape((4,3)), columns=list('bde'), index=['Utah', 'Ohio', 'Texas', 'Oregon'])
series = frame.ix[0]
```
```apple js
frame - series = DataFrame({'b':[0,3,6,9],'d':[0,3,6,9],'e':[0,3,6,9]}, index=['Utah', 'Ohio', 'Texas', 'Oregon'])
```
是因为逐行传播的原因，frame每一行的元素都会减去series的对应值。

如果某个索引值在DataFrame的列或者Series的索引中找不到，则参与运算的两个对象就会被重新索引形成并集
### 总结
本篇内容总结了DataFrame和Series的关系和核心概念。更多的详细内容需要参考《利用python进行数据分析》
本篇文章只是作为学习笔记使用。
