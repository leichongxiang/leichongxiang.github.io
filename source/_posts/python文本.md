title: python文本
date: 2015-12-24 12:00:35
tags:
 - python
categories: [python]

---

深入理解python
# 处理单个字符串
```python
In [2]: thelist = list("hello")

In [3]: thelist
Out[3]: ['h', 'e', 'l', 'l', 'o']

results = [upper(c) for c in "hello"]
In [8]: results
Out[8]: ['H', 'E', 'L', 'L', 'O']

In [11]: def upper(c):
   ....:     return c.upper()
   ....: 

In [12]:  results = map( upper , "hello")

In [13]: results
Out[13]: ['H', 'E', 'L', 'L', 'O']
```
<!--more -->

# 字符串反转
```python
In [27]: str = "my name is vike"

In [28]: revwords = ' '.join(str.split()[::-1])

In [29]: revwords
Out[29]: 'vike is name my'

In [34]: revwords = ''.join(re.split(r'(\s+)',str)[::-1]) 

In [35]: revwords
Out[35]: 'vike is name my'
```
# 字符串的标记
标识符被放在一对括弧中，括弧前面一个%，后面一个s。然后还需要使用%操作符，
使用的格式是将需要处理的字符串放在%的左边，并在右边放上字典。
```python
In [1]: old_style = 'this is %(thing)s'

In [2]: print old_style % { 'thing' : 5}
this is 5

In [3]: print old_style % { 'thing' : "test"}
this is test

```
# 设置颜色
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

class bcolors:
    HEADER = '\033[31m'
    OKBLUE = '\033[34m'
    OKGREEN = '\033[32m'
    WARNING = '\033[33m'
    FAIL = '\033[35m'
    ENDC = '\033[0m'
    BOLD = '\033[1m'
    UNDERLINE = '\033[4m'

print bcolors.OKBLUE + "警告的颜色字体" + bcolors.ENDC
```

# collections
collections是Python内建的一个集合模块，提供了许多有用的集合类。

## namedtuple

我们知道tuple可以表示不变集合，例如，一个点的二维坐标就可以表示成：
`In [8]: p = (1,2) `看到这个元组，很难知道是表示坐标的。namedtuple就派上用场。

```python
# namedtuple('名称', [属性list]):
In [10]: from collections import *      

In [11]: point = namedtuple('point',['x','y'])

In [13]: p = point(1,2)

In [14]: p.x
Out[14]: 1

In [15]: p.y
Out[15]: 2
```

## deque

使用list存储数据时，按索引访问元素很快，但是插入和删除元素就很慢了，因为list是线性存储，数据量大的时候，插入和删除效率很低。

deque是为了高效实现插入和删除操作的双向列表，适合用于队列和栈：
```python
>>> from collections import deque
>>> q = deque(['a', 'b', 'c'])
>>> q.append('x')
>>> q.appendleft('y')
>>> q
deque(['y', 'a', 'b', 'c', 'x'])
```
deque除了实现list的append()和pop()外，还支持appendleft()和popleft()，这样就可以非常高效地往头部添加或删除元素。

## defaultdict

使用dict时，如果引用的Key不存在，就会抛出KeyError。如果希望key不存在时，返回一个默认值，就可以用defaultdict：
```python
In [3]: from collections import *

In [4]: dd = defaultdict(lambda: 'N/A')

In [5]: dd['key1'] = 'abc'

In [6]: dd['key1']
Out[6]: 'abc'

In [7]: dd['key2']
Out[7]: 'N/A'

```
## OrderedDict

使用dict时，Key是无序的。在对dict做迭代时，我们无法确定Key的顺序。

如果要保持Key的顺序，可以用OrderedDict：
```python
>>> from collections import OrderedDict
>>> d = dict([('a', 1), ('b', 2), ('c', 3)])
>>> d # dict的Key是无序的
{'a': 1, 'c': 3, 'b': 2}
>>> od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
>>> od # OrderedDict的Key是有序的
OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```
注意，OrderedDict的Key会按照插入的顺序排列，不是Key本身排序：
```python
>>> od = OrderedDict()
>>> od['z'] = 1
>>> od['y'] = 2
>>> od['x'] = 3
>>> list(od.keys()) # 按照插入的Key的顺序返回
['z', 'y', 'x']
```
OrderedDict可以实现一个FIFO（先进先出）的dict，当容量超出限制时，先删除最早添加的Key：
```python
from collections import OrderedDict

class LastUpdatedOrderedDict(OrderedDict):

    def __init__(self, capacity):
        super(LastUpdatedOrderedDict, self).__init__()
        self._capacity = capacity

    def __setitem__(self, key, value):
        containsKey = 1 if key in self else 0
        if len(self) - containsKey >= self._capacity:
            last = self.popitem(last=False)
            print('remove:', last)
        if containsKey:
            del self[key]
            print('set:', (key, value))
        else:
            print('add:', (key, value))
        OrderedDict.__setitem__(self, key, value) 
```

## Counter

Counter是一个简单的计数器，例如，统计字符出现的个数：
```python
>>> from collections import Counter
>>> c = Counter()
>>> for ch in 'programming':
...     c[ch] = c[ch] + 1
...
>>> c
Counter({'g': 2, 'm': 2, 'r': 2, 'a': 1, 'i': 1, 'o': 1, 'n': 1, 'p': 1})
```

# 构建一个多值的字典

```
In [17]: d.setdefault('a',[]).append(1)

In [18]: d.setdefault('a',[]).append(2)

In [19]: d.setdefault('b',[]).append(4)

In [20]: d
Out[20]: {'a': [1, 2], 'b': [4]}
```
构建多值字典
```
pairs = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d = {}
for key, value in pairs:
if key not in d:
d[key] = []
d[key].append(value)

In [34]: d

Out[34]: {'blue': [2, 4], 'red': [1], 'yellow': [1, 3]}

```
也可以使用其他方法
```
pairs = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d = defaultdict(list)
for key, value in pairs:
    d[key].append(value)

print list(d.items())
[('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
```

