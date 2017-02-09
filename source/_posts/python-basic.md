---
title: python time和datetime模块
date: 2017-02-09 17:47:44
tags:
categories: python
---
python 中有很多常见基础的模块，比如time，datetime，经常会遇到。
需要总结记录一下，为以后能后提高编码效率做一些准备。
### time

- time.time()  #获取当前时间的时间戳，时间戳最适合做时期运算，范围是1970~2038年
- time.localtime(timestamp)  #输入时间戳，返回时间戳对应的时间元组
- time.mktime(tupletime)  #输入时间元组，返回对应的时间戳
- time.asctime(tupletime)  #输入时间元组，返回对应的时间字符串
- time.ctime(timestamp)  #输入时间戳，返回对应的时间字符串

### datetime

datetime是一种时间类型
```apple js
>>> print(datetime.datetime.now())
>>> 2017-02-09 19:55:10.123111
```
- datetime.datetime.now()  #获取当前datetime,类型是datetime
- datetime.date.today()  #获取当天date

datetime和timestamp类型的相互转化
```apple js
#datetime -> timestamp
dt = datetime(2015,4,19,12,20)
timestamp = dt.timestamp()
#timestamp -> datetime
t= 1429417200.0
datetime =  datetime.formatstamp(t)
```
如果已经有了datetime对象，要把它格式化为字符转显示给用户，就要转化为str，
转换方法是通过strftime()实现的，同样需要一个日期和时间的格式化字符串
```apple js
now = datetime.datetime.now()
print(now.strftime('%a, %b %d %h:%M'))
```
datetime 加减
对日期和时间进行加减实际上就是把datetime往后或往前计算，得到新的datetime。
加减可以直接用+和-运算符，不过需要导入timedelta这个类：
```apple js
>>> from datetime import datetime, timedelta
>>> now = datetime.now()
>>> now
datetime.datetime(2015, 5, 18, 16, 57, 3, 540997)
>>> now + timedelta(hours=10)
datetime.datetime(2015, 5, 19, 2, 57, 3, 540997)
>>> now - timedelta(days=1)
datetime.datetime(2015, 5, 17, 16, 57, 3, 540997)
>>> now + timedelta(days=2, hours=12)
datetime.datetime(2015, 5, 21, 4, 57, 3, 540997)
```
