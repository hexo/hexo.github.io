---
title: python multiprocessing模块
date: 2017-02-10 19:21:08
tags:
categories: python
---
python中的多线程其实并不是真正的多线程，如果想要充分地使用多核CPU的资源，在python中大部分情况需要使用多进程。
Python提供了非常好用的多进程包multiprocessing，只需要定义一个函数，Python会完成其他所有事情。
借助这个包，可以轻松完成从单进程到并发执行的转换。multiprocessing支持子进程、通信和共享数据、执行不同形式的同步
，提供了Process、Queue、Pipe、Lock等组件。
### 创建进程
```
import multiprocessing
import time

def worker(interval):
    n = 5
    while n > 0:
        print("Worker->The time is {0}".format(time.ctime()))
        time.sleep(interval)
        n -= 1

def worker_2(interval):
    n = 5
    while n > 0:
        print("Worker_2->The time is {0}".format(time.ctime()))
        time.sleep(interval)
        n -= 1

def worker_3(interval):
    n = 5
    while n > 0:
        print("Worker_3->The time is {0}".format(time.ctime()))
        time.sleep(interval)
        n -= 1

if __name__ == '__main__':
    p = multiprocessing.Process(target=worker, args=(1,))
    p2 = multiprocessing.Process(target=worker_2, args=(1,))
    p3 = multiprocessing.Process(target=worker_3, args=(1,))
    p.start()
    p2.start()
    p3.start()
    p.join()
    p2.join()
    p3.join()
    print("pid:", p.pid, p2.pid, p3.pid)
    print("name: ", p.name, p2.name, p3.name)
    print("p.is_alive: ", p.is_alive(), p2.is_alive(), p3.is_alive())
    print("The number of CPU is:" + str(multiprocessing.cpu_count()))
```
```
import multiprocessing
import time

class ClockProcessing(multiprocessing.Process):
    """Use multiprocessing.Process class to realize mutiprocess.

    We will overide the __init__ and run function to realize it.

    """
    def __init__(self, interval):
        super(ClockProcessing, self).__init__()
        self.interval = interval

    def run(self):
        n = 5
        while n > 0:
            print("the time is {0}".format(time.ctime()))
            time.sleep(self.interval)
            n -= 1

if __name__ == '__main__':
    p = ClockProcessing(3)
    #set daemon = True. It is called守护进程
    #if daemon = True.When main Process Finished.
    # the subprocess will be finished without finishing it's work
    p.daemon = True
    p.start()
    #p.join()
    print("Processing Finished!")
```
### 使用进程锁实现互斥资源访问
当多个进程需要访问共享资源的时候，Lock可以用来避免访问的冲突。
```
import multiprocessing

def work_with(lock, f):
    with lock:
        fs = open(f, 'a+')
        n = 10
        while n > 1:
            fs.write("Lock acquired via with\n")
            n -= 1
        fs.close()

def work_no_with(lock, f):
    lock.acquire()
    try:
        fs = open(f, 'a+')
        n = 10
        while n > 1:
            fs.write("Lock acquired directly\n")
            n -= 1
        fs.close()
    finally:
        lock.release()

if __name__ == '__main__':
    lock = multiprocessing.Lock()
    f = "file.txt"
    w = multiprocessing.Process(target=work_with, args=(lock, f))
    nw = multiprocessing.Process(target=work_no_with, args=(lock, f))
    w.start()
    nw.start()
    print("End")

```
### 使用信号量实现有限资源调度
Semaphore用来控制对共享资源的访问数量，例如池的最大连接数
```
import multiprocessing
import time

def worker(s, i):
    s.acquire()
    print(multiprocessing.current_process().name + "acquire")
    time.sleep(i)
    print(multiprocessing.current_process().name + "release")
    s.release()

if __name__ == '__main__':
    s = multiprocessing.Semaphore(2)
    for i in range(5):
        p = multiprocessing.Process(target=worker, args=(s, i*2))
        p.start()
```
### 使用队列实现相同资源的访问
Queue是多进程安全的队列，可以使用Queue实现多进程之间的数据传递。put方法用以插入数据到队列中，
put方法还有两个可选参数：blocked和timeout。如果blocked为True（默认值），并且timeout为正值，
该方法会阻塞timeout指定的时间，直到该队列有剩余的空间。如果超时，会抛出Queue.Full异常。
如果blocked为False，但该Queue已满，会立即抛出Queue.Full异常。
 
get方法可以从队列读取并且删除一个元素。同样，get方法有两个可选参数：blocked和timeout。如果bl
ocked为True（默认值），并且timeout为正值，那么在等待时间内没有取到任何元素，会抛出Queue.Empty异
常。如果blocked为False，有两种情况存在，如果Queue有一个值可用，则立即返回该值，否则，如果队列为空
，则立即抛出Queue.Empty异常。

```
"""We can use queue to realize multiprocess's data communication

"""
import multiprocessing

def writer_proc(q):
    try:
        q.put(1, block=False)
    except:
        pass

def reader_proc(q):
    try:
        print(q.get(block=False))
    except:
        pass

if __name__ == '__main__':
    q = multiprocessing.Queue()
    writer = multiprocessing.Process(target=writer_proc, args=(q,))
    writer.start()
    reader = multiprocessing.Process(target=reader_proc, args=(q,))
    reader.start()

    reader.join()
    writer.join()
```
### 使用pipe实现同时访问
Pipe方法返回(conn1, conn2)代表一个管道的两个端。Pipe方法有duplex参数，如果duplex参数为True(默认值)，
那么这个管道是全双工模式，也就是说conn1和conn2均可收发。duplex为False，conn1只负责接受消息，conn2只负
责发送消息。
 
send和recv方法分别是发送和接受消息的方法。例如，在全双工模式下，可以调用conn1.send发送消息，conn1.recv
接收消息。如果没有消息可接收，recv方法会一直阻塞。如果管道已经被关闭，那么recv方法会抛出EOFError
``` 
import multiprocessing
import time

def proc1(pipe):
    while True:
        for i in range(5):
            print("send: %s" %(i))
            pipe.send(i)
            time.sleep(1)

def proc2(pipe):
    while True:
        print("proc2 rev:", pipe.recv())
        time.sleep(1)

def proc3(pipe):
    while True:
        print("PROC3 rev:", pipe.recv())
        time.sleep(1)

if __name__ == "__main__":
    pipe = multiprocessing.Pipe()
    p1 = multiprocessing.Process(target=proc1, args=(pipe[0],))
    p2 = multiprocessing.Process(target=proc2, args=(pipe[1],))
    #p3 = multiprocessing.Process(target=proc3, args=(pipe[1],))

    p1.start()
    p2.start()
    #p3.start()

    p1.join()
    p2.join()
    #p3.join()
```
### 使用Event实现消息等待
Event用来实现进程间同步通信
``` 
"""Use Event to realize process synchronize.

We have two mode.wait_for_event and wait_for_event_timeout
if no_timeout the process will be blocked until e.set() is executed.
"""

import multiprocessing
import time

def wait_for_event(e):
    print("wait_for_event: starting")
    e.wait()
    print("wait_for_event: e.is_set()->" + str(e.is_set()))

def wait_for_event_timeout(e, t):
    print("Wait_for_event_timeout:starting")
    e.wait(t)
    print("wait_for_event_timeout: e.is_set()->" + str(e.is_set()))

if __name__ == '__main__':
    e = multiprocessing.Event()
    w1 = multiprocessing.Process(name="block",
                                 target=wait_for_event, args=(e,))
    w2 = multiprocessing.Process(name="non-block",
                                 target=wait_for_event_timeout, args=(e, 2))
    w1.start()
    w2.start()

    time.sleep(5)

    e.set()
    print("main: event is set")
```
### 使用进程池
在利用Python进行系统管理的时候，特别是同时操作多个文件目录，或者远程控制多台主机，并行操作可以节约大量的时间
。当被操作对象数目不大时，可以直接利用multiprocessing中的Process动态成生多个进程，十几个还好，但如果是上百个
，上千个目标，手动的去限制进程数量却又太过繁琐，此时可以发挥进程池的功效。

Pool可以提供指定数量的进程，供用户调用，当有新的请求提交到pool中时，如果池还没有满，那么就会创建一个新的进程用
来执行该请求；但如果池中的进程数已经达到规定最大值，那么该请求就会等待，直到池中有进程结束，才会创建新的进程来它。
``` 
"""this module use Pool to realize multiprocess"""

import multiprocessing
import time

def func(msg):
    print("msg:", msg)
    time.sleep(3)
    print("end", msg)

if __name__ == '__main__':
    pool = multiprocessing.Pool(processes=3)
    for i in range(4):
        msg = "hello %d " %(i)
        #apply_async 非阻塞
        #apply 阻塞，当进入第一个进程时会将后续进程阻塞，直到当前进程运行结束
        pool.apply_async(func, (msg, ))
    print("Mark,Mark,Mark")
    pool.close()
    pool.join()
    print("SubProcess done..")

```