title: python的装饰器
date: 2015-12-01 18:53:35
tags:
 - 装饰器
categories: [python]
---

首先说一个编码中尽量应该遵守的封闭开放原则：它规定已经实现的功能代码不允许被修改，但可以被扩展。
假设一个场景，需要给已经实现的业务功能前添加一个验证功能，那么需要怎么做呢，这时候批量改每个函数功能的代码是不可行，这时装饰器就可以正式登场了。

<!--more -->

# 装饰器的简单实例

```python
def w1(func):
    def inner():
        print 'auth'
        return func()
    return inner

@w1
def f1():
    print 'f1'

f1()

#结果
auth
f1
```

# 说明原理
首先编译器在函数在没有被调用之前其内部代码不会被执行。
`@w1 这一句代码里却有大文章，@函数名 是python的一种语法糖。@注解语法糖- Syntactic Sugar `

@w1会做两件事：
* 执行w1函数，并将 @w1 下面的 函数 作为w1函数的参数，即：@w1 等价于 w1(f1)
* 将执行完的 w1 函数返回值赋值给@w1下面的函数的函数名

放在例子中应该第一步是：
>所以，内部就会去执行：
def inner:
	#验证
	return f1()   # func是参数，此时 func 等于 f1
return inner     # 返回的 inner，inner代表的是函数，非执行函数
其实就是将原来的 f1 函数塞进另外一个函数中

第二步：
>w1函数的返回值是：
def inner:
	#验证
	return 原来f1()  # 此处的 f1 表示原来的f1函数
然后，将此返回值再重新赋值给 f1，即：
新f1 = def inner:
		#验证
		return 原来f1() 
所以，以后业务部门想要执行 f1 函数时，就会执行 新f1 函数，在 新f1 函数内部先执行验证，再执行原来的f1函数，然后将 原来f1 函数的返回值 返回给了业务调用者。
如此一来， 即执行了验证的功能，又执行了原来f1函数的内容，并将原f1函数返回值 返回给业务调用着

# 对参数数量不确定的函数进行装饰


```python
def deco(func):
    def _deco(*args, **kwargs):
        print("before %s called." % func.__name__)
        ret = func(*args, **kwargs)
        print("  after %s called. result: %s" % (func.__name__, ret))
        return ret
    return _deco
 
@deco
def myfunc(a, b):
    print(" myfunc(%s,%s) called." % (a, b))
    return a+b
 
@deco
def myfunc2(a, b, c):
    print(" myfunc2(%s,%s,%s) called." % (a, b, c))
    return a+b+c
 
myfunc(1, 2)
myfunc2(1, 2, 3)


#结果
before myfunc called.
myfunc(1,2) called.
after myfunc called. result: 3
before myfunc2 called.
myfunc2(1,2,3) called.
after myfunc2 called. result: 6
```

# 多个装饰器
多装饰器就是盒子模型
```python
def w1(func):
    def inner():
	    print 'w1,before'
		func()
		print 'w1,after'
	return inner

def w2(func):
    def inner():
	    print 'w2,before'
		func()
		print 'w2,after'
	return inner

@w2
@w1
def foo():
    print 'foo'
	
foo()

#结果
w2,before
w1,before
foo
w1,after
w2,after

```


# 返回值不定的装饰器
```python

def auth(func):
    def innner(*args,**kwargs):
	    print 'before'
		temp = func(*args,**kwargs)
		print 'after'
		return temp
	return inner
	
@auth
def fetch_server_list(arg):
    server_list = ['c1','c2','c3']
	return server_list
```

# 带参数的装饰器
```python
#!/usr/bin/env python
#coding:utf-8
  
def Before(request,kargs):
    print 'before'
      
def After(request,kargs):
    print 'after'
  
  
def Filter(before_func,after_func):
    def outer(main_func):
        def wrapper(request,kargs):
              
            before_result = before_func(request,kargs)
            if(before_result != None):
                return before_result;
              
            main_result = main_func(request,kargs)
            if(main_result != None):
                return main_result;
              
            after_result = after_func(request,kargs)
            if(after_result != None):
                return after_result;
              
        return wrapper
    return outer

#在执行index函数之前，执行一个函数，在执行函数之后执行另外一个函数；例如：在之前输出时间，在执行完成之后输出时间和日志	
@Filter(Before, After)
def Index(request,kargs):
    print 'index'

```

