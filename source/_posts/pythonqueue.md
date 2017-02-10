---
title: python queue模块
date: 2017-02-10 10:46:53
tags:
categories: python
---
### queue介绍

queue是python中的标准库，俗称队列，可以直接import 引用，在python2.x中，模块名为Queue
在python中，多个线程之间的数据是共享的，多个线程进行数据交换的时候，不能够保证数据的安全性和一致性，所以当多个线程需要进行数据交换的时候，队列就出现了，队列可以完美解决线程间的数据交换，保证线程间数据的安全性和一致性
### queue模块有三种队列及构造函数:

- Python queue模块的FIFO队列先进先出。 class queue.Queue(maxsize)
- LIFO类似于堆，即先进后出。 class queue.LifoQueue(maxsize)
- 还有一种是优先级队列级别越低越先出来。 class queue.PriorityQueue(maxsize)

### queue模块中的常用方法:
```
queue.qsize() 返回队列的大小
queue.empty() 如果队列为空，返回True,反之False
queue.full() 如果队列满了，返回True,反之False
queue.full 与 maxsize 大小对应
queue.get([block[, timeout]])获取队列，timeout等待时间
queue.get_nowait() 相当queue.get(False)
queue.put(item) 写入队列，timeout等待时间
queue.put_nowait(item) 相当queue.put(item, False)
queue.task_done() 在完成一项工作之后，queue.task_done()函数向任务已经完成的队列发送一个信号
queue.join() 实际上意味着等到队列为空，再执行别的操作
```
