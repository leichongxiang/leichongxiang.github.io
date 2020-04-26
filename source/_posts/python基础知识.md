title: python基础知识
date: 2015-10-28 16:53:35
tags:
 - python
categories: [python]

---

# str与repr区别

repr语法：repr[object]

返回一个可以表示对象的可打印的字符串，首先会生成一个这样的字符串，然后`将其传给eval()可以重新生成同样的对象`。但是repr所返回的对象更适合于解释器去阅读。
`在python中还有一个``这个反引号和repr()`作用一样。

str语法：str[objec]
返回一个可以表示对象的友好的可打印的字符串。对于字符串则返回本身，如果没有参数，则返回空字符串。str返回的对象更适合我们人类阅读（可以这么理解），str致力于返回一个可读性比较好的对象，返回的结果通常`不会通过eval()去处理`。

<!--more -->
实例：

```python
>>> a = 'Hello,kitty!'
>>> str(a)
'Hello,kitty!'            #字符串str会返回本身
>>> repr(a)
"'Hello,kitty!'"
>>> a = 'Hello,kitty!\n'
>>> b = repr(a)
>>> print b
'Hello,kitty!\n'
>>> c = str(a)
>>> print c
Hello,kitty!
```
应用：

```python
>>> tmp=42
>>> print "the temperature is " + tmp
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot concatenate 'str' and 'int' objects

>>> print "the temperature is " + `tmp`
the temperature is 42
```

# input()与raw_input()区别
使用input和raw_input都可以读取控制台的输入，但是input和raw_input在处理数字时是有区别的
纯数字输入
当输入为纯数字时

    input返回的是数值类型，如int,float
    raw_inpout返回的是字符串类型，string类型
输入字符串为表达式

input会计算在字符串中的数字表达式，而raw_input不会。

例子1：
```python
>>> raw_input_A = raw_input("raw_input: ")
raw_input: efg

>>> input_A = input("Input: ")
Input: efg
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 1, in <module>
NameError: name 'efg' is not defined

>>> input_A = input("Input: ")
Input: "efg"
```
思考：
但 raw_input() 直接读取控制台的输入（任何类型的输入它都可以接收）。而对于 input() ，它希望能够读取一个合法的 python 表达式，即你输入字符串的时候必须使用引号将它括起来

例子2：

```python
>>> raw_input_A = raw_input("raw_input: ")
raw_input: 123
>>> input_A = input("Input: ")
Input: 123
```
input( 1 + 3 ) 会返回 int 型的 4

# 序列
python中有6中内建的序列：列表，元组，字符串，unicode字符串，buffer对象，xrange对象。

![序列](/img/p_01.jpg)

如果想创建一个占用10个元素的空间，却不包括任何有用的内容的列表：
sequence = [None] * 10

检查一个值是否在序列中用`in`

## 列表
列表跟元组的区别在于，列表是可变的。列表功能强大
![列表]](/img/p_02.jpg)

### 转换为列表的方法list
list函数适用于所有类型的序列，不只是字符串
```python
>>> list('hello')
['h', 'e', 'l', 'l', 'o']
>>> list('1234')
['1', '2', '3', '4']
```

### 列表操作
列表除通用的序列操作外还有自己的操作

#### 赋值
```python
>>> x=[1]*3
>>> x
[1, 1, 1]
>>> x[1]=2
>>> x
[1, 2, 1]
```

#### 删除元素
del name[3]

#### 分片赋值
可以用与原序列不等长的序列将分片替换
```python
>>> name = list('Perl')
>>> name[1:] = list('ython')
>>> name
['P', 'y', 't', 'h', 'o', 'n']
```

### 列表方法
对象.方法(参数)
#### append
```python
>>> a =[1,2,3]
>>> a.append(4)
>>> a
[1, 2, 3, 4]
```

#### count
统计某个元素在列表中出现的次数

```python
>>> x = [[1,2],1,1,[2,1,[1,2]]]
>>> x.count(1)
2
>>> x.count([1,2])
1
```

#### extend

`注意extend与连接（+）的区别`
extend：是在源列表中修改的；
连接+：会返回一个全新的列表；
所以：extend要比连接的操作效率高

```python
#extend
>>> a=[1,2,3]
>>> b=[4,5,6]
>>> a.extend(b)
>>> a
[1, 2, 3, 4, 5, 6]
#连接
>>> a=[1,2,3]
>>> b=[4,5,6]
>>> a+b
[1, 2, 3, 4, 5, 6]
>>> a
[1, 2, 3]
```

#### index
index方法：从列表中找出某个值第一次匹配的索引的位置。
```python
>>> a=['one','two','three']
>>> a.index('two')
1
```

#### insert
```python
>>> a = [1, 2, 3, 4,6]
>>> a.insert(4,'five')
>>> a
[1, 2, 3, 4, 'five', 6]
```

#### pop
pop是唯一一个既能修改列表又能返回元素值的列表方法
```python
>>> a = [1,2,3]
>>> a.pop()
3
>>> a
[1, 2]
>>> a.pop(0)
1
```

#### remove
remove：移除列表中某一个值第一个匹配项

#### reverse
```python
>>> a = [1, 2, 3]
>>> a.reverse()
>>> a
[3, 2, 1]
```
也可以用序列通用方法：list(reversed(x))

#### sort
`注意:sort方法用于在原位置对列表进行排序，改变原来的列表，返回None`

```python
>>> a = [1, 9, 3, 7,6]
>>> a.sort()
>>> a
[1, 3, 6, 7, 9]
```

问题：当用户需要一个排好序的列表副本，同时保留原列表不变的做法
错误：
```python
>>> a = [1, 9, 3, 7,6]
>>> b = a.sort()
>>> print b
None
```
`注意：b = a.sort()返回是None，后边不能再跟任何方法x.sort().reverse()是不对的`

正确：

方法一：先把a的副本赋值给b，然后对b排序
```python
>>> a = [1, 9, 3, 7,6]
>>> b = a[:]
>>> b.sort()
>>> a
[1, 9, 3, 7, 6]
>>> b
[1, 3, 6, 7, 9]
```
`注意：列表赋值b = a与b = a[:]的区别`
b = a：a,b都指向同一个列表

方法二：sorted函数
```python
>>> a = [1, 9, 3, 7,6]
>>> b = sorted(a)
>>> a
[1, 9, 3, 7, 6]
>>> b
[1, 3, 6, 7, 9]
```
`注意：sorted函数可以用于任何序列，却总是返回一个列表`
```python
>>> sorted('python')
['h', 'n', 'o', 'p', 't', 'y']
```

### 高级排序
按照特定的方式进行排序,首先需要定义好规则函数，然后函数提供给sort作为参数
```python
>>> a = [1, 9, 3, 7,6]
>>> a.sort(cmp)
>>> a
[1, 3, 6, 7, 9]
```

sort有另外两个可选参数key和reverse

参数key与参数cmp类似：
```python
#按照元素长度排序
>>> a = ['yuinhfs','abc','b0','yunv','u','abcdf']
>>> a.sort(key=len)
>>> a
['u', 'b0', 'abc', 'yunv', 'abcdf', 'yuinhfs']
```
reverse参数
```python
>>> a = [1, 9, 3, 7,6]
>>> a.sort(reverse=True)
>>> a
[9, 7, 6, 3, 1]
```

## 元组
元组为不可变序列
包括一个元素的元组：
```python
#错误
>>> 24
24
#错误
>>> (24)
24
#正确
>>> (24,)
(24,)
```

tuple类型转化
```python
>>> tuple([1,2,3])
(1, 2, 3)
>>> tuple('abc')
('a', 'b', 'c')
```
