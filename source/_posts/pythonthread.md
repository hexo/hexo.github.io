---
title: python threading模块
date: 2017-02-10 11:23:21
tags:
categories: python
---
线程是cpu分配的基本单位，同一时刻单个cpu中只会运行一个线程。而进程则是资源分配的基本单位
不同的进程之间资源是不共享的，而同一进程的多个线程之间是共享相同的资源，所以就涉及到访问顺序权限的问题。
下面从五个实例中介绍python中线程以及多线程并发同步的实现

### 传入目标函数方式创建线程
```
import threading
import time

def test_thread(count):
    while count > 0:
        print("count:", count)
        count -= 1
        time.sleep(1)

def main():
    """threading 可以通过直接实例化父类threading.

    Thread 来实现，传入target函数.
    args 参数元组来调用生成，这种方式更加灵活

    """
    mythread = threading.Thread(target=test_thread, args=(10,))
    mythread.start()
    mythread.join()

if __name__ == '__main__':
    main()

```
### 重写父类threading.Thread方法创建线程
```
import  threading
import logging
import time

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class Mythread(threading.Thread):
    """
    属性：
    target：传入外部函数，用户线程调用
    args：函数参数
    """
    def __init__(self, target, args):
        logger.info("调用父类的构造函数")
        super(Mythread, self).__init__() #调用父类的构造函数
        logger.debug("Mythread %s", Mythread)
        self.target = target
        self.args = args

    def run(self):
        logger.info("执行run函数")
        self.target(self.args)

def print_time(counter):
    while counter :
        print("counter = %d" % counter)
        counter -= 1
        time.sleep(1)
    logger.info("打印结束")
def main():
    logger.info("进入主函数")
    my_thread = Mythread(print_time, 10)
    logger.info("线程准备开始")
    my_thread.start()
    start = time.time()
    my_thread.join()
    spend = time.time() - start
    logger.info("spend %s 秒", spend)

if __name__ == '__main__':
    main()
```
### 多线程的实现
```
import threading
import time

class MyThread(threading.Thread):
    """生成MyThread子类.
    """
    def __init__(self, thread_id, name, counter):
        super(MyThread, self).__init__()
        self.thread_id = thread_id
        self.name = name
        self.counter = counter

    def run(self):
        print("starting", self.name)
        print_time(self.name, self.counter, 5)
        print("exiting", self.name)

def print_time(thread_name, delay, counter):
    while counter:
        time.sleep(delay)
        print("%s %s" % (thread_name, time.ctime(time.time())))
        counter -= 1

if __name__ == '__main__':
    thread1 = MyThread(1, "Thread-1", 1)
    thread2 = MyThread(2, "Thread-2", 2)
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()
    print("exit main thread")
```
### 通过加锁的方式使用资源
```
import threading
import time


threadLock = threading.Lock()

class myThread(threading.Thread):
    """thread synchronize.

    we use thread lock to ensure the thread synchronize

    Args:
        threadID: the thread's identifier
        name: the thread's name
        counter: the thread's runtime counter

    """


    def __init__(self, threadID, name, counter):
        super(myThread, self).__init__()
        self.threadID = threadID
        self.name = name
        self.counter = counter

    def run(self):
        print("starting" + self.name)
        #get the lock ,return True when success
        #return Flase when timeout
        threadLock.acquire()
        print_time(self.name, self.counter, 3)
        #release the lock
        threadLock.release()

def print_time(threadName, delay, counter):
    while counter:
        time.sleep(delay)
        print("%s: %s" % (threadName, time.ctime(time.time())))
        counter -= 1

def main():
    threads = []
    #new threads
    thread1 = myThread(1, "Thread-1", 1)
    thread2 = myThread(2, "Thread-2", 2)
    #start new thread
    thread1.start()
    thread2.start()
    #add to list
    threads.append(thread1)
    threads.append(thread2)
    #wait finish
    for t in threads:
        t.join()
    print("Exiting main Thread")

if __name__ == '__main__':
    main()
```
### 使用queue实现进程同步
```
"""use Queue to realize the queue priority.
"""
import threading
import time
import queue

exitFlag = 0
threadList = ["thread-1", "Thread-2", "Thread-3"]
nameList = ["One", "Two", "Three", "Four", "Five"]
queueLock = threading.Lock()
workQueue = queue.Queue(10)
threads = []
threadID = 1

class myThread(threading.Thread):
    """realize the thread queue priority.
    """
    def __init__(self, threadID, name, q):
        super(myThread, self).__init__()
        self.threadID = threadID
        self.name = name
        self.q = q

    def run(self):
        print("Starting " + self.name)
        process_data(self.name, self.q)
        print("Exiting " + self.name)

def process_data(threadName, q):
    while not exitFlag:
        queueLock.acquire()
        if not workQueue.empty():
            data = q.get()
            queueLock.release()
            print("%s processing %s" % (threadName, data))
        else:
            queueLock.release()
        time.sleep(1)

def main():
    #如果内部函数引用外部函数的同名变量或者全局变量，并且对这个变量有修改，
    #那么Pyhton会认为他是一个局部变量
    #本函数中会认为threadID是局部变量，在未定义时使用，会报错
    #因此要使用global 声明threadID是全局变量即可
    global threadID
    global exitFlag
    #generalize new thread
    for tName in threadList:
        thread = myThread(threadID, tName, workQueue)
        thread.start()
        threads.append(thread)
        threadID += 1

    #fill the queue
    queueLock.acquire()
    for word in nameList:
        workQueue.put(word)
    queueLock.release()

    #wait for queue empty
    while not workQueue.empty():
        pass

    #info for thread to exit
    exitFlag = 1

    #wait thread exit
    for t in threads:
        t.join()
    print("Exiting Main Thread")

if __name__ == '__main__':
    main()
```
以上就是关于队列，线程的相关内容，便于以后需要时查阅。队列的相关内容在上一篇博客中记录