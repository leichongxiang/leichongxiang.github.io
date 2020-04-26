title: python的魔法方法
date: 2015-11-10 14:53:35
tags:
 - 魔法方法_python
categories: [python]

---

魔法方法用前后双下划线的方式标示

# 构造函数（__init__）
当一个对象被创建时，会立即调用构造方法。
```python

>>> class Rectangle:
...     def __init__(self,x,y):
...         self.x = x
...         self.y = y
...     def getperl(self):
...         return (self.x + self.y)*2
...     def getarea(self):
...         return self.x * self.y
... 

#结果
>>> rec = Rectangle(3,4)
>>> rec.getperl()
14
>>> rec.getarea()
12

```
构造函数的实质是在最初的调用__new__函数

```python
>>> class Capstr(str):   
...     def __new__(cls,string):
...         string = string.upper()
...         return str.__new__(cls,string)
... 

#结果
>>> a = Capstr("i am vike")
>>> a
'I AM VIKE'
```

<!--more -->

# 析构函数（__del__）
当没有引用的时候，才会调用析构函数。

```python
>>> class C:
...     def __init__(self):
...         print("我是__init__方法，我被调用了。。。")
...     def __del__(self):
...         print("我是__del__方法，我被调用了。。。") 
... 


#结果
>>> c1 = C()
我是__init__方法，我被调用了。。。
>>> c2 = c1
>>> c3 = c2
>>> del c3
>>> del c2
>>> del c1
我是__del__方法，我被调用了。。。
```


