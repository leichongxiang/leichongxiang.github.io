title: python中queue
date: 2015-11-25 19:50:02
tags:
 - queue
categories: [python]

---
Queue类即是一个队列的同步实现。队列长度可为无限或者有限。可通过Queue的构造函数的可选参数maxsize来设定队列长度。如果maxsize小于1就表示队列长度无限。
```python
创建一个 队列 对象 最大长度为10
from Queue import Queue
q = Queue(maxsize = 10)
 
import Queue
q = Queue.Queue(maxsize = 10)
```
<!--more -->

# python queue模块有三种队列:
1. python queue模块的FIFO队列先进先出。其构造函数
   class Queue.Queue(maxsize)  
2. LIFO类似于堆。即先进后出。其构造函数
   class Queue.LifoQueue(maxsize)   
3. 还有一种是优先级队列级别越低越先出来。其构造函数 
   class Queue.PriorityQueue(maxsize)   

# Queue的常用方法:
   Queue.qsize() #返回队列的大小 
   Queue.empty() #如果队列为空,返回True,反之False 
   Queue.full()  #如果队列满了,返回True,反之False
   Queue.full 与 maxsize 大小对应 
   Queue.get([block[, timeout]]) #获取队列,timeout等待时间,调用队列对象的get()方法从队头删除并返回一个项目。可选参数为block,默认为True。如果队列为空且block为True,get()就使调用线程暂停,直至有项目可用。如果队列为空且block为False,队列将引发Empty异常。 
   Queue.get_nowait() #相当Queue.get(False)
   Queue.put(item)    #非阻塞写入队列,timeout等待时间,调用队列对象的put()方法在队尾插入一个项目。
   put()有两个参数,第一个item为必需的,为插入项目的值；第二个block为可选参数,默认为1。如果队列当前为空且block为1,put()方法就使调用线程暂停,直到空出一个数据单元。如果block为0,put方法将引发Full异常。
   Queue.put_nowait(item) #相当Queue.put(item, False)
   Queue.task_done()   #在完成一项工作之后,Queue.task_done() 函数向任务已经完成的队列发送一个信号Queue.join() 实际上意味着等到队列为空,再执行别的操作.

# 实例
如下代码实现了比较经典的生产者和消费者模型：
```python

from Queue import Queue
import random
import threading
import time

#Producer thread
class Producer(threading.Thread):
    def __init__(self, t_name, queue):
        threading.Thread.__init__(self, name=t_name)
        self.data=queue
    def run(self):
        for i in range(5):
            print "%s: %s is producing %d to the queue!" %(time.ctime(), self.getName(), i)
            self.data.put(i)
            time.sleep(random.randrange(10)/5)
        print "%s: %s finished!" %(time.ctime(), self.getName())

#Consumer thread
class Consumer(threading.Thread):
    def __init__(self, t_name, queue):
        threading.Thread.__init__(self, name=t_name)
        self.data=queue
    def run(self):
        for i in range(5):
            val = self.data.get()
            print "%s: %s is consuming. %d in the queue is consumed!" %(time.ctime(), self.getName(), val)
            time.sleep(random.randrange(10))
        print "%s: %s finished!" %(time.ctime(), self.getName())

#Main thread
def main():
    queue = Queue()
    producer = Producer('Pro.', queue)
    consumer = Consumer('Con.', queue)
    producer.start()
    consumer.start()
    producer.join()
    consumer.join()
    print 'All threads terminate!'
 
if __name__ == '__main__':
    main()

程序输出
[root@rac1 python]# python prdcust.py   
start consumer
start producer
producing...1
producing...2
producing...3
producing...4
producing...5
5
consuming...4
consuming...3
consuming...2
consuming...1
consuming...0
0
0
```
thread模块中的方法，下一节学。
getName()
返回线程名。

setName(name)
设置线程名。   
   
   
   