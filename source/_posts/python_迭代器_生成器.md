title: python迭代器和生成器
date: 2015-11-09 18:50:35
tags:
 - python
 - 迭代器和生成器
categories: [python]

---

# 迭代器
迭代器：就是具有next方法的对象。在调用next方法时，迭代器会返回它的下一个值；当没有值可以返回时，会引发一个StopIteration异常。

```python
>>> def gennum(n):
...     i = 1
...     while i <= n:
...         yield i**2
...         i +=1
... 
>>> g1=gennum(5)
>>> g1.next()
1
>>> g1.next()
4
>>> g1.next()
9
>>> g1.next()
16
>>> g1.next()
25
>>> g1.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

```

<!--more -->


# 生成器
生成器：任何包含yield语句的函数都是生成器，它会返回一个生成器对象。`生成器本本身都是可以迭代的`

```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

def gennum(n):
    i = 1
    while i <= n:
        yield i**2
        i +=1

g1 = gennum(10)

for i in g1:
    print i

	
#结果
[root@backup py_basic]# ./py_yield.py 
1
4
9
16
25
36
49
64
81
100
```
# 装饰器

装饰器：
* 本身是一个函数，目的是装饰其他函数
* 功能：增强被调用函数的功能
装饰器一般接受一个函数对象作为参数，进行包装
	
```python
>>> def deco(func):
...     def wrapper():
...         print "P1: say something:"
...         func()
...         print "No zuo no die..."
...     return wrapper
... 
>>> @deco
... def show():
...     print "i am from Mars"
... 
>>> show()
P1: say something:
i am from Mars
No zuo no die...

```

# python的闭包
外层函数为内层函数提供环境，内层环境在调用外层变量时，在内存中会记录，即使返回下次使用也可以用，除非重新赋值

```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

def f1(n):
    def f2(m):
        return m**n
    return f2

mi = f1(3)
type(mi) #返回的是个函数
print mi(2),mi(3),mi(4)

#结果
[root@backup py_basic]# ./py_close.py 
8 27 64
```
很多情景都能用到，比如棋盘的棋子
```python
>>> def startpos(m,n):
...     def newpos(x,y):
...         print "old postion is (%d,%d),new postion is (%d,%d)" % (m,n,m+x,n+y)
...     return newpos

>>> action = startpos(10,10)
>>> action(1,2)
old postion is (10,10),new postion is (11,12)
>>> action(3,4)
old postion is (10,10),new postion is (13,14)
>>> action(-3,-4)
old postion is (10,10),new postion is (7,6)

```






	

