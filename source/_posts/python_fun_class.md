title: python函数和类
date: 2015-11-02 18:53:35
tags:
 - func_class_python
categories: [python]

---

python中抽象的的办法：函数和类。

# 函数
## 简单的函数使用
用一个斐波那契数列代码说明函数
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

def fibs(num):
    result = [0,1]
    for i in range(num-2):
        result.append(result[-2]+result[-1])
    return result

def main():
    n = int(raw_input('how many Fionacci nuimbers do you want? '))
    print fibs(n)

main()

[root@backup py_basic]# ./fibs.py
how many Fionacci nuimbers do you want? 10
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

```
<!--more -->

## 收集参数
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

def params01(*params):
    print params

def params02(title,*params):
    print title
    print params

def params03(**params):
    print params

def params04(x,y,z=1,*pospar,**keypar):
    print x,y,z
    print pospar
    print keypar

def main():
    params01('Test') ##('Test',)说明前面加上一个星号是元组
    params01(1,2,3)   ##(1, 2, 3)
    params02('params:',1,2,3)
    ##params:
    ##(1, 2, 3)
    params03(x=1,y=2,z=3) ##{'y': 2, 'x': 1, 'z': 3}
    params04(1,2,3,5,6,7,foo=1,bar=2)
    ##1 2 3
    ##(5, 6, 7)
    ##{'foo': 1, 'bar': 2}
main()

#结果
('Test',)
(1, 2, 3)
params:
(1, 2, 3)
{'y': 2, 'x': 1, 'z': 3}
1 2 3
(5, 6, 7)
{'foo': 1, 'bar': 2}

```
`注意`:
python中如果出现中文，需要在`第一行（即#!/usr/bin/env python下一行）添加代码#-*- coding: utf-8 -*-`
def params01(*params):说明前面加上一个星号是元组，收集其余位置的参数
def params03(**params):说明前面加上两个星号是字典，收集键值类型的参数

## 递归
通过两个实例来说明递归用法.

实例1：阶乘
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

def factorial(num):
    result = num
    for i in range(1,num):
        result *= i
    return result

def main():
    n = int(raw_input('how many Factorial nuimbers do you want? '))
    print factorial(n)

main()
#结果
[root@backup py_basic]# ./factorial.py
how many Factorial nuimbers do you want? 4
24
```

实例2：二分查找法
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

def search(lists,num,lower=0,upper=None):
    if upper is None: upper = len(lists)-1
    if lower == upper:
        assert num == lists[upper]
        return upper
    else:
        middle = (lower+upper)//2
        if num > lists[middle]:
            return search(lists,num,middle+1,upper)
        else:
            return search(lists,num,lower,middle)

if __name__ == '__main__':
    seq =[4, 8, 15, 17, 26, 33, 47, 59, 65, 74]
    print search(seq,17)

#结果
[root@backup py_basic]# ./search.py
3
```


