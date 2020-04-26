title: python中set
date: 2015-11-27 19:50:02
tags:
 - set
categories: [python]

---
set和queue这两种经典的数据结构, 集与队列.今天主要介绍set的用法
<font  color="red">set的内部结构和dict很像，唯一区别是不存储value，</font>因此，判断一个元素是否在set中速度很快。
<font  color="red">set存储的元素和dict的key类似，必须是不变对象，</font>因此，任何可变对象是不能放入set中的。
最后，set存储的元素也是没有顺序的。

<!--more -->
# 遍历set
```python
weekdays = set(['MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT', 'SUN'])
 
for d in weekdays:
    print (d)
```

访问set
由于set存储的是无序集合，所以我们没法通过索引来访问。
**访问 set中的某个元素实际上就是判断一个元素是否在set中。**
```python
s = set(['Adam', 'Lisa', 'Bart', 'Paul'])
'Bart' in s
#True
'Bill' in s
#False
```
# set常用的方法
len(s)
set 的长度

x in s
测试 x 是否是 s 的成员

x not in s
测试 x 是否不是 s 的成员

s.issubset(t)
s <= t
测试是否 s 中的每一个元素都在 t 中

s.issuperset(t)
s >= t
测试是否 t 中的每一个元素都在 s 中

s.union(t)
s | t
返回一个新的 set 包含 s 和 t 中的每一个元素

s.intersection(t)
s & t
返回一个新的 set 包含 s 和 t 中的公共元素

s.difference(t)
s - t
返回一个新的 set 包含 s 中有但是 t 中没有的元素

s.symmetric_difference(t)
s ^ t
返回一个新的 set 包含 s 和 t 中不重复的元素

s.copy()
返回 set “s”的一个浅复制

hash(s)
   返回 s 的 hash 值
下面这个表列出了对于 Set 可用二对于 ImmutableSet 不可用的运算：
运算符（voperator）
等价于
运算结果

s.update(t)
s |= t
返回增加了 set “t”中元素后的 set “s”

s.intersection_update(t)
s &= t
返回只保留含有 set “t”中元素的 set “s”

s.difference_update(t)
s -= t
返回删除了 set “t”中含有的元素后的 set “s”

s.symmetric_difference_update(t)
s ^= t
返回含有 set “t”或者 set “s”中有而不是两者都有的元素的 set “s”

s.add(x)
向 set “s”中增加元素 x

s.remove(x)
从 set “s”中删除元素 x, 如果不存在则引发 KeyError

s.discard(x)
如果在 set “s”中存在元素 x, 则删除

s.pop()
删除并且返回 set “s”中的一个不确定的元素, 如果为空则引发 KeyError

s.clear()
删除 set “s”中的所有元素

