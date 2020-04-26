title: python生成器
date: 2016-02-17 16:53:35
tags:
 - python
categories: [python]
photos:
 - http://ww4.sinaimg.cn/large/8e712944jw1f12hj8o2zxj2064064glh.jpg
---

## 使用dict和set
在使用dict和list的时候，经常会遇到字典不存的时候报错。
dict：
如果key不存在，dict就会报错：

```
>>> d['Thomas']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Thomas'

```
要避免key不存在的错误，有两种办法，一是通过in判断key是否存在：

```
>>> 'Thomas' in d
False

```
二是通过dict提供的get方法，如果key不存在，可以返回None，或者自己指定的value：
```
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1

注意：返回None的时候Python的交互式命令行不显示结果。
```
<!--more -->

## 列表生成式

```
>>> L = [] >>> for x in range(1, 11): 
    L.append(x * x)
等价于
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：
```
>>> [x * x for x in range(1, 11) if x % 2 == 0] 
[4, 16, 36, 64, 100]
```
还可以使用两层循环，可以生成全排列：

```
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

## 生成器
通过列表生成式，我们可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。

所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。

要创建一个generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator：

```
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>

创建L和g的区别仅在于最外层的[]和()，L是一个list，而g是一个generator。
```

正确访问生成器的方法是：==使用for循环，因为generator也是可迭代对象：==

```
>>> g = (x * x for x in range(10)) 
>>> for n in g: 
        print(n)
```
## 著名的斐波拉契数列（Fibonacci）

```
#!/usr/bin/env python

def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    #return 'done' #不能这么写，原因如下   
    return

f = fib(10)
#print('fib(10):', f)
for x in f:
    print(x)
```

==yield中return的作用==：

作为生成器，因为每次迭代就会返回一个值，所以不能显示的在生成器函数中return 某个值，包括None值也不行，否则会抛出“SyntaxError”的异常，但是在函数中可以出现单独的return，表示结束该语句。
通过固定长度的缓冲区不断读文件，防止一次性读取出现内存溢出的例子：

## 练习题杨辉三角

```
#!/usr/bin/env python

def trangles(n):
    a = [1]
    while n > 0: 
        yield a 
        a = [sum(i) for i in zip([0] + a, a + [0])]
        n = n -1

for line in trangles(5):
    print line
    
#结果
[root@backup py_basic]# ./trangles.py 
[1]
[1, 1]
[1, 2, 1]
[1, 3, 3, 1]
[1, 4, 6, 4, 1]
```

说明：
通过观察杨辉三角可知，下一行的每一个元素都依次由本行中每两个相邻元素之和得到，这个方法可以用一个技巧实现，即：将本行list拷贝出两个副本，将两个副本错1位，然后加在一起。由于错位后，前后各多了一个元素，所以要在错位后的两个list的前后各加一个[0]来补齐（其实，这个0是理所当然的，是杨辉三角的一部分）。

同一行中前后相邻两个元素相加（这是杨辉三角的构成规则），就相当于两个本行元素错位相加。而zip方法，就是从这两行中分别取出第i个位置的元素组成元组（这也是添“0”的原因）。sum()函数正好求出它们的和，进而求出了下一行。然后又yield函数把这一行“塞入”generator--也就是本例中的triangles()。

## 总结
要理解generator的工作原理，它是在for循环的过程中不断计算出下一个元素，并在适当的条件结束for循环。对于函数改成的generator来说，遇到return语句或者执行到函数体最后一行语句，就是结束generator的指令，for循环随之结束。