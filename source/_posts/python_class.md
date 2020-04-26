title: python的类
date: 2015-11-03 18:53:35
tags:
 - class_python
categories: [python]

---

# 类中常见的一些函数
callable(object)    		确定对象是否可调用
hasattr(object,name)		确定对象是否有给定的特性
isinstance(object,class)	确定对象是否是类的实例
issubcleass(A,B) 			确定A是否为B的子类
random.choice(sequence)		从非空序列中随机选择元素

<!--more -->

# 私有方法
为了让方法或者特性变为私有（从外部无法访问），只要在它的名字前面加上双下划线：
```python
#!/usr/bin/env python

class Secretive:
    def __a(self):
        print "Bet you can't see me."
    def b(self):
        print "The secret message is:"
        self.__a()

s = Secretive()
s.__a()

[root@backup py_basic]# ./py_class.py  
Traceback (most recent call last):
  File "./py_class.py", line 11, in <module>
    s.__a()
AttributeError: Secretive instance has no attribute '__a'

```

其实私有化方法真正发生的事情：类的内部定义中，所有以双下划线开始的名字都被“翻译”成前面加上`单下划线和类名的形式`。
```python
s = Secretive()
s._Secretive__a()

[root@backup py_basic]# ./py_class.py  
Bet you can't see me.

```

# 异常类Except
引发异常： raise
捕获异常： try: .... except:....
else子句： 如果try块中没有引发异常，else子句就会被执行
finally：  如果需要确保某些代码不管是否有异常引发都要执行，那么这些代码可以放置在finally子句中。
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

while True:
    try:
        x = input('Enter the frist number: ')
        y = input('Enter the second number: ')
        value = x/y
        print 'x/y is ', value
    except Exception,e:
        print 'Invalid input:',e
        print 'Please try again'
    else:
        break
		
#结果
[root@backup py_basic]# ./py_except.py 
Enter the frist number: 1
Enter the second number: 0
Invalid input: integer division or modulo by zero
Please try again
Enter the frist number: 'x'
Enter the second number: 'y'
Invalid input: unsupported operand type(s) for /: 'str' and 'str'
Please try again
Enter the frist number: 10
Enter the second number: 2
x/y is  5		

```
综合所有异常语句的例子
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

try:
    1/0
except NameError:
    print "Unknown variable"
else:
    print "that went well"
finally：
    print "Cleaning up"

```
