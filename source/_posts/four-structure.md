---
layout: python
title: python 四种数据结构
date: 2017-05-08 16:00:55
tags:
---
Python中list,tuple,dict,set的区别和用法
Python语言简洁明了，可以用较少的代码实现同样的功能。这其中Python的四个内置数据类型功不可没，他们即是list, tuple, dict, set。这里对他们进行一个简明的总结。

 

List

字面意思就是一个集合，在Python中List中的元素用中括号[]来表示，可以这样定义一个List:

L = [12, 'China', 19.998]
可以看到并不要求元素的类型都是一样的。当然也可以定义一个空的List:

L = []
 

Python中的List是有序的，所以要访问List的话显然要通过序号来访问，就像是数组的下标一样，一样是下标从0开始：
```
print L[0]
12
千万不要越界，否则会报错

print L[3]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
List也可以倒序访问，通过“倒数第x个”这样的下标来表示序号，比如-1这个下标就表示倒数第一个元素：

L = [12, 'China', 19.998]
print L[-1]
19.998
-4的话显然就越界了

print L[-4]

Traceback (most recent call last):
  File "<pyshell#2>", line 1, in <module>
    print L[-4]
IndexError: list index out of range
```
 

List通过内置的append()方法来添加到尾部，通过insert()方法添加到指定位置（下标从0开始）：
```
>>> L = [12, 'China', 19.998]
>>> L.append('Jack')
>>> print L
[12, 'China', 19.998, 'Jack']
>>> L.insert(1, 3.14)
>>> print L
[12, 3.14, 'China', 19.998, 'Jack']
>>> 
```
通过pop()删除最后尾部元素，也可以指定一参数删除指定位置：
```
>>> L.pop()
'Jack'
>>> print L
[12, 3.14, 'China', 19.998]
>>> L.pop(0)
12
>>> print L
[3.14, 'China', 19.998]
```
也可以通过下标进行复制替换
```
>>> L[1] = 'America'
>>> print L
[3.14, 'America', 19.998]
```

Tuple

Tuple可以看做是一种“不变”的List，访问也是通过下标，用小括号（）表示：
```
>>> t = (3.14, 'China', 'Jason')
>>> print t
(3.14, 'China', 'Jason')
```
但是不能重新赋值替换：
```
>>> t[1] = 'America'

Traceback (most recent call last):
  File "<pyshell#21>", line 1, in <module>
    t[1] = 'America'
TypeError: 'tuple' object does not support item assignment
```
也没有pop和insert、append方法。

可以创建空元素的tuple:

t = ()
或者单元素tuple (比如加一个逗号防止和声明一个整形歧义):

t = (3.14,)
 

那么tuple这个类型到底有什么用处呢？要知道如果你希望一个函数返回多个返回值，其实只要返回一个tuple就可以了，因为tuple里面的含有多个值，而且是不可变的（就像是java里面的final）。当然，tuple也是可变的，比如:
```
>>> t = (3.14, 'China', 'Jason', ['A', 'B'])
>>> print t
(3.14, 'China', 'Jason', ['A', 'B'])
>>> L = t[3]
>>> L[0] = 122
>>> L[1] = 233
>>> print t
(3.14, 'China', 'Jason', [122, 233])
```
这是因为Tuple所谓的不可变指的是指向的位置不可变，因为本例子中第四个元素并不是基本类型，而是一个List类型，所以t指向的该List的位置是不变的，但是List本身的内容是可以变化的，因为List本身在内存中的分配并不是连续的。

 

Dict

Dict是Python中非常重要的数据类型，就像它的字面意思一样，它是个活字典，其实就是Key-Value键值对，类似于HashMap，可以用花括号{}通过类似于定义一个C语言的结构体那样去定义它：
```
>>> d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59,
    'Paul': 75
}
>>> print d
{'Lisa': 85, 'Paul': 75, 'Adam': 95, 'Bart': 59}
```
可以看到打印出来的结果都是Key:Value的格式，可以通过len函数计算它的长度（List，tuple也可以）：
```
>>> len(d)
4
```

可以直接通过键值对方式添加dict中的元素：
```
>>> print d
{'Lisa': 85, 'Paul': 75, 'Adam': 95, 'Bart': 59}
>>> d['Jone'] = 99
>>> print d
{'Lisa': 85, 'Paul': 75, 'Adam': 95, 'Jone': 99, 'Bart': 59}
 
```
List和Tuple用下标来访问内容，而Dict用Key来访问： (字符串、整型、浮点型和元组tuple都可以作为dict的key)
```
>>> print d['Adam']
95
```
如果Key不存在，会报错：
```
>>> print d['Jack']

Traceback (most recent call last):
  File "<pyshell#40>", line 1, in <module>
    print d['Jack']
KeyError: 'Jack'
```
所以访问之前最好先查询下key是否存在：
```
>>> if 'Adam' in d : print 'exist key'

exist key
```
或者直接用保险的get方法：
```
>>> print d.get('Adam')
95
>>> print d.get('Jason')
None
```

至于遍历一个dict，实际上是在遍历它的所有的Key的集合，然后用这个Key来获得对应的Value:
```
>>> for key in d : print key, ':', d.get(key)

Lisa : 85
Paul : 75
Adam : 95
Bart : 59
```

Dict具有一些特点：
查找速度快。无论是10个还是10万个，速度都是一样的，但是代价是耗费的内存大。List相反，占用内存小，但是查找速度慢。这就好比是数组和链表的区别，数组并不知道要开辟多少空间，所以往往开始就会开辟一个大空间，但是直接通过下标查找速度快；而链表占用的空间小，但是查找的时候必须顺序的遍历导致速度很慢
没有顺序。Dict是无顺序的，而List是有序的集合，所以不能用Dict来存储有序集合
Key不可变，Value可变。一旦一个键值对加入dict后，它对应的key就不能再变了，但是Value是可以变化的。所以List不可以当做Dict的Key，但是可以作为Value:

```
>>> print d
{'Lisa': 85, 'Paul': 75, 'Adam': 95, 'Jone': 99, 'Bart': 59}
>>> d['NewList'] = [12, 23, 'Jack']
>>> print d
{'Bart': 59, 'NewList': [12, 23, 'Jack'], 'Adam': 95, 'Jone': 99, 'Lisa': 85, 'Paul': 75}
Key不可重复。（下面例子中添加了一个'Jone':0，但是实际上原来已经有'Jone'这个Key了，所以仅仅是改了原来的value）
>>> print d
{'Bart': 59, 'NewList': [12, 23, 'Jack'], 'Adam': 95, 'Jone': 99, 'Lisa': 85, 'Paul': 75}
>>> d['Jone'] = 0
>>> print d
{'Bart': 59, 'NewList': [12, 23, 'Jack'], 'Adam': 95, 'Jone': 0, 'Lisa': 85, 'Paul': 75}
```

Dict的合并，如何将两个Dict合并为一个，可以用dict函数：
```
>>> d1 = {'mike':12, 'jack':19}
>>> d2 = {'jone':22, 'ivy':17}
>>> dMerge = dict(d1.items() + d2.items())
>>> print dMerge
{'mike': 12, 'jack': 19, 'jone': 22, 'ivy': 17}
或者
>>> dMerge2 = dict(d1, **d2)
>>> print dMerge2
{'mike': 12, 'jack': 19, 'jone': 22, 'ivy': 17}
方法2比方法1速度快很多，方法2等同于：
>>> dMerge3 = dict(d1)
>>> dMerge3.update(d2)
>>> print dMerge
{'mike': 12, 'jack': 19, 'jone': 22, 'ivy': 17
```

set
set就像是把Dict中的key抽出来了一样，类似于一个List，但是内容又不能重复，通过调用set()方法创建：
```
>>> s = set(['A', 'B', 'C'])
```
就像dict是无序的一样，set也是无序的，也不能包含重复的元素。
对于访问一个set的意义就仅仅在于查看某个元素是否在这个集合里面：
```
>>> print 'A' in s
True
>>> print 'D' in s
False
```
大小写是敏感的。

也通过for来遍历：
```
s = set([('Adam', 95), ('Lisa', 85), ('Bart', 59)])
#tuple
for x in s:
    print x[0],':',x[1]

>>>
Lisa : 85
Adam : 95
Bart : 59
```

通过add和remove来添加、删除元素（保持不重复），添加元素时，用set的add()方法：
```
>>> s = set([1, 2, 3])
>>> s.add(4)
>>> print s
set([1, 2, 3, 4])
```
如果添加的元素已经存在于set中，add()不会报错，但是不会加进去了：
```
>>> s = set([1, 2, 3])
>>> s.add(3)
>>> print s
set([1, 2, 3])
```
删除set中的元素时，用set的remove()方法：
```
>>> s = set([1, 2, 3, 4])
>>> s.remove(4)
>>> print s
set([1, 2, 3])
```
如果删除的元素不存在set中，remove()会报错：
```
>>> s = set([1, 2, 3])
>>> s.remove(4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 4
```

所以如果我们要判断一个元素是否在一些不同的条件内符合，用set是最好的选择，下面例子：
```
months = set(['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec',])
x1 = 'Feb'
x2 = 'Sun'

if x1 in months:
    print 'x1: ok'
else:
    print 'x1: error'

if x2 in months:
    print 'x2: ok'
else:
    print 'x2: error'

>>>
x1: ok
x2: error
```