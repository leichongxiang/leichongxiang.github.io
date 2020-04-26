title: python字符串和字典
date: 2015-10-30 16:53:35
tags:
 - python
 - dict
 - str
categories: [python]

---

这篇博文主要总结一下python中字符串和字典使用需要注意的问题。

# 字符串
## 格式化字符串
如果要在格式化字符串里边包含百分号，那么必须使用%%
```python
>>> format = "hello ,%s,%s for %% you"
>>> value = ("ao%wo","hot")
>>> print format % value
hello ,ao%wo,hot for % you
```
<!--more -->

## 字符串方法
### join
被连接的序列元素都必须是字符串
```python

>>> seq = [1,2,3,4,5]
>>> op = '+'
>>> op.join(seq)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: sequence item 0: expected string, int found

>>> seq = ['1','2','3','4','5']
>>> op = '+'
>>> op.join(seq)
'1+2+3+4+5'
```
### split
它跟join方法是逆方法
```python
>>> '1+2+3+4+5'.split('+')
['1', '2', '3', '4', '5']
```

### strip
strip方法返回去除两侧空格的字符串。还有lstrip,rstrip
```python
#!/usr/bin/env python

names = ['vike','alen','jake']

name = raw_input('please input your name:')
if name.strip().lower() in names:
    print 'Fount it'
```

### translate和replace
translate:只处理单个字符，有时候比replease效率高

```python
>>> 'this is a test'.replace('is','eez')
'theez eez a test'
```

```python
>>> 'this is a test'.translate('i','e')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: translation table must be 256 characters long

#translate转换之前必须先完成一张转换表，maketrans接受两个等长字符串

>>> from string import maketrans
>>> table = maketrans('is','ez')
>>> 'this is a test'.translate(table)
'thez ez a tezt'
```
# 字典
字典是python中唯一内建的映射类型。字典中的值没有特殊的顺序，但是都是存在一个特定的键下。键可以是任何`不可变`类型，如数字，字符串，甚至元组。
![字典](/img/p_03.jpg)

成员资格：
k in dict (dict是字典)，查找的是键不是值；
v in list (list是列表)，查找的是值不是索引；

## 字典的格式化字符串
使用字典格式化字符串时，需要用`%(键)`这种方式
```python
>>> phonebook={'vike':'1234','alen':'0102','jake':'0345'}
>>> "vike's phone number is %(vike)s." % phonebook
"vike's phone number is 1234."

```
## 字典的方法

### clear
clear方法清除字典中的所有项。`这是个原地操作`（类似于list.sort()）
```python
#情况1
>>> x = {}
>>> y = x
>>> x['key'] = 'value'
>>> y
{'key': 'value'}
>>> x = {}
>>> y
{'key': 'value'}

#情况2
>>> x = {}
>>> y = x
>>> x['key'] = 'value'
>>> y
{'key': 'value'}
>>> x.clear()
>>> y
{}

```
说明，清空原始字典必须用clear方法，clear是对原字典操作的。

### copy && deepcopy
copy返回一个具有相同键-值对的新字典,`浅复制`
```python
>>> x = {'username':'admin','machines':['foo','bar','baz']}
>>> y = x.copy()
>>> y['username'] = 'vike'
>>> y['machines'].remove('bar')
>>> y
{'username': 'vike', 'machines': ['foo', 'baz']}
>>> x
{'username': 'admin', 'machines': ['foo', 'baz']}

```
当副本y在替换值的时候，原字典不受影响；
当副本y在修改值的时候，原字典也会改变；
为了避免这个问题，需要用deepcopy`深拷贝`

```python
>>> from copy import deepcopy
>>> x = {'username':'admin','machines':['foo','bar','baz']}
#深拷贝
>>> y = deepcopy(x)
>>> y['username'] = 'vike'
>>> y['machines'].remove('bar')
>>> y
{'username': 'vike', 'machines': ['foo', 'baz']}
>>> x
{'username': 'admin', 'machines': ['foo', 'bar', 'baz']}

```
### fromkeys
fromkeys:使用指定的键建立新的字典。

```python
>>> dict.fromkeys(['name','age'])
{'age': None, 'name': None}

>>> dict.fromkeys(['name','age'],'(unknown)')
{'age': '(unknown)', 'name': '(unknown)'}

```
### get
get：访问字典项的方法。
当使用get访问一个不存在的键时，没有任何异常，而是得到None或者自定义的值。

```python
>>> d = {}
>>> print d['name']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'name'
>>> print d.get('name')
None
>>>
>>> print d.get('name','N/A')
N/A
```
### has_key
检查是否存在某个特定的键值，相当于 k in d.
```python
d = {}
d.has_key('name')
```
### keys,values,items
keys && iterkeys:将字典的键以列表的形式返回；iterkeys：会返回一个迭代器对象
```python
>>> d = {1:'one',2:'two',3:'three'}
>>> d.keys()
[1, 2, 3]

>>> it = d.iterkeys()
>>> it
<dictionary-keyiterator object at 0x7f435e6db940>
>>> list(it)
[1, 2, 3]
```
values && itervalues:将字典的值以列表的形式返回；itervalues：会返回一个迭代器对象
items && iteritems:将字典的所有项以列表的形式返回；iteritems：会返回一个迭代器对象

```python
>>> d = {1:'one',2:'two',3:'three'}
>>> d.values()
['one', 'two', 'three']
>>> d.keys()
[1, 2, 3]
>>> d.items()
[(1, 'one'), (2, 'two'), (3, 'three')]
```
### pop
pop：获取给定键的值，然后移除这个键值
```python
>>> d = {1:'one',2:'two',3:'three'}
>>> d.pop(2)
'two'
>>> d
{1: 'one', 3: 'three'}

```
### popitem
如果想一个接一个的移除并处理字典中的项的话，popitem很有效。
```python
>>> d = {1:'one',2:'two',3:'three'}
>>> d.popitem()
(1, 'one')
>>> d
{2: 'two', 3: 'three'}

```

### setdefault
当键不存在的时候，setdefault返回默认值并且更新字典；
当键存在的时候，返回与其对应的值，但不改变字典；
```python
>>> d = {}
>>> d.setdefault('name','N/A')
'N/A'
>>> d
{'name': 'N/A'}
>>> d['name'] = 'vike'
>>> d.setdefault('name','N/A')
'vike'
>>> d
{'name': 'vike'}
```

### update
update：利用一个字典去更新另一个字典
```python
>>> d = {1:'one',2:'two',3:'three'}
>>> x = {2:'**'}
>>> d.update(x)
>>> d
{1: 'one', 2: '**', 3: 'three'}
```
# 其他语句
## is
同一性运算符，跟==服不一样
```python
>>> x = y = [1,2,3]
>>> z = [1,2,3]
>>> x == y
True
>>> x == z
True
>>> x is y
True
>>> x is z
False

```
因为is是判断同一性而不是相同性的。变量x和y都被绑定到同一个列表上，z被绑定在另外一个具有相同数值和顺序的列表上。
## range
range函数，它包括下限，但不包括上限
```python
>>> range(0,5)
[0, 1, 2, 3, 4]
```
range:一次创建整个序列；
xrange：一次只创建一个数，当需要迭代一个巨大的序列时xrange会更高效

## zip
zip函数：并行迭代的
```python
>>> names = ['vike','alen','jake']
>>> age = [27,25,30]
>>> zip(names,age)
[('vike', 27), ('alen', 25), ('jake', 30)]

```
`zip可以处理不等长序列`
```python
>>> zip(range(5),xrange(100000))
[(0, 0), (1, 1), (2, 2), (3, 3), (4, 4)]

#想想为什么用了xrange(100000)
```
## enumerate
枚举，下标从0开始
```python
>>> a = enumerate(names)
>>> list(a)
[(0, 'vike'), (1, 'alen'), (2, 'jake')]

#实例
for index , name in enumerate(names):
     if 'alen' in name:
         names[index] = 'Alen'
print names
['vike', 'Alen', 'jake']
```
## pass
pass: 调试代码，什么都不做
```python
if name == 'vike':
    print 'Welcome'
elif name == 'alen':
    #还没做
    pass

```

## exec
exec：执行一个字符串的语句，最有用的地方是可以动态的创建代码字符串
```python
>>> exec "print 'hello world'"
hello world
```
## eval
eval：用于求值，类似于exec的内建函数。exec语句会执行一系列python语句，而eval会计算python的表达式。
```python
>>> eval(raw_input("Enter an arithmetic expression: "))
Enter an arithmetic expression: 4+6*7
46

```


# 列表推导式

```python
>>> [x*x for x in range(10)]
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> [x*x for x in range(10) if x % 3 == 0]
[0, 9, 36, 81]

>>> [(x,y) for x in range(3) for  y in range(3)]
[(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
```

