title: python模块快速入门
date: 2015-11-11 18:50:35
tags:
 - python
 - 模块
categories: [python]

---

# time
通常有这几种方式来表示时间
* 时间戳 
* 格式化的时间字符串
* 元组（struct_time）共九个元素，（年，月，日，时，分，秒，周，一年中的第几天，夏令时）

## 特殊函数说明
time.time() 获取当前时间戳
time.localtime() 当前时间的struct_time形式
time.ctime() 当前时间的字符串形式

strftime(...)
  strftime(format[, tuple]) -> string
  将指定的struct_time(默认为当前时间)，根据指定的格式化字符串输出
  
clock()
  clock() -> floating point number
  该函数有两个功能，
  在第一次调用的时候，返回的是程序运行的实际时间；
  以第二次之后的调用，返回的是自第一次调用后,到这次调用的时间间隔

<!--more -->

## 实例


```python

>>> import time
>>> time.time()
1447315221.7882249

>>> time.localtime()
time.struct_time(tm_year=2015, tm_mon=11, tm_mday=12, tm_hour=16, tm_min=0, tm_sec=32, tm_wday=3, tm_yday=316, tm_isdst=0)

>>> FORMAT='%Y-%m-%d %H:%M:%S'
>>> time.strftime(FORMAT,time.localtime())
'2015-11-12 16:09:34'

```

# random
Python中的random模块用于生成随机数

## 特殊函数解释
random.uniform的函数原型为：random.uniform(a, b)，用于生成一个指定范围内的随机符点数
random.randint()的函数原型为：random.randint(a, b)，用于生成一个指定范围内的整数
random.randrange的函数原型为：random.randrange([start], stop[, step])，从指定范围内，按指定基数递增的集合中 获取一个随机数。
random.choice从序列中获取一个随机元素。
random.shuffle的函数原型为：random.shuffle(x[, random])，用于将一个列表中的元素打乱
random.sample的函数原型为：random.sample(sequence, k)，从指定序列中随机获取指定长度的片断

## 实例
```python
>>> import random
>>> random.uniform(10,20)
13.632064644779806
>>> random.randint(10,20)
14
>>> random.randrange(10,20,2)
18
>>> random.randrange(10,20)  
16
>>> random.randrange(10,20)
15
>>> random.choice ( ['apple', 'pear', 'peach', 'orange', 'lemon'] )
'peach'
>>> items = [1, 2, 3, 4, 5, 6]
>>> random.shuffle(items) 
>>> items
[4, 5, 6, 3, 1, 2]
>>> random.sample('abcdefghij',3)
['e', 'b', 'c']
```

# shelve
存储数据

## 实例
通过网上的一个典范例子来理解

```python
#python shelve

#Author : Hongten
#MailTo : hongtenzone@foxmail.com
#QQ     : 648719819
#Blog   : http://www.cnblogs.com/hongten
#Create : 2013-08-09
#Version: 1.0

import shelve
'''
    python中的shelve模块，可以提供一些简单的数据操作
    他和python中的dbm很相似。

    区别如下：
    都是以键值对的形式保存数据，不过在shelve模块中，
    key必须为字符串，而值可以是python所支持的数据
    类型。在dbm模块中，键值对都必须为字符串类型。

    sh['a'] = 'a'
    sh['c'] = [11, 234, 'a']
    sh['t'] = ('1', '2', '3')
    sh['d'] = {'a':'2', 'name':'Hongte' }
    sh['b'] = 'b'
    sh['i'] = 23

    我们可以获取一个shelve对象
    sh = shelve.open('c:\\test\\hongten.dat', 'c')

    删除shelve对象中的某个键值对
    del sh['d']

    遍历所有数据
    for item in sh.items():
        print('键[{}] = 值[{}]'.format(item[0], sh[item[0]]))

    获取某个键值对
    print(sh['a'])

    关闭shelve对象:
    sh.close()
    
    ####################################################
    ####        API中强调
    Do not rely on the shelf being closed automatically;
    always call close() explicitly when you don’t need
    it any more, or use a with statement with
    contextlib.closing().
    ####################################################

'''
#global var
#是否显示日志信息
SHOW_LOG = True

def get_shelve():
    '''open -- file may get suffix added by low-level library'''
    return shelve.open('c:\\test\\hongten.dat', 'c')

def save(sh):
    '''保存数据'''
    if sh is not None:
        sh['name'] = 'Hongten'
        sh['gender'] = 'M'
        sh['address'] = {'hometown' : 'Shuifu,Yunnan', 'nowadd' : 'Guangzhou,Guangdong'}
        sh['phone'] = ('13423****62', '18998****62')
        sh['age'] = 22
        if SHOW_LOG:
            for item in sh.items():
                print('保存数据[{}] = [{}]'.format(item[0], sh[item[0]]))
        sh.close()
    else:
        print('the shelve object is None!')

def update(sh):
    '''更新数据'''
    if sh is not None:
        sh['name'] = 'Hongten'
        sh['hoby'] = ('篮球', '羽毛球', '乒乓球', '游泳')
        sh['phone'] = ('13423****62', '18998****62', '020-90909090')
        sh['age'] = 23
        if SHOW_LOG:
            keys = ('name', 'hoby', 'phone', 'age')
            for item in keys:
                print('更新数据[{}] = [{}]'.format(item, sh[item]))
        sh.close()
    else:
        print('the shelve object is None!')

def delete(sh, key):
    '''删除某个数据'''
    if sh is not None:
        if SHOW_LOG:
            print('删除[{}]的数据'.format(key))
        del sh[key]
        sh.close()
    else:
        print('the shelve object is None!')

def deleteall(sh):
    '''删除所有数据'''
    if sh is not None:
        for item in sh.items():
            if SHOW_LOG:
                print('删除数据[{}] = [{}]'.format(item[0], sh[item[0]]))
            del sh[item[0]]
        sh.close()
    else:
        print('the shelve object is None!')

def fetchone(sh, key):
    '''获取某个数据'''
    if sh is not None:
        print('获取[{}]的值：{}'.format(key, sh[key]))
        sh.close()
    else:
        print('the shelve object is None!')

def fetchall(sh):
    '''遍历所有数据'''
    if sh is not None:
        for item in sh.items():
            print('数据[{}] = [{}]'.format(item[0], sh[item[0]]))
        sh.close()
    else:
        print('the shelve object is None!')

###############################################################
###                测试           START
###############################################################
def save_test():
    '''保存数据...'''
    print('保存数据...')
    sh = get_shelve()
    save(sh)

def fetchall_test():
    '''遍历所有数据'''
    print('遍历所有数据...')
    sh = get_shelve()
    fetchall(sh)

def fetchone_test():
    '''获取某个数据'''
    print('获取某个数据...')
    sh = get_shelve()
    key = 'address'
    fetchone(sh, key)

def delete_test():
    '''删除某个数据'''
    print('删除某个数据...')
    sh = get_shelve()
    key = 'hoby'
    delete(sh, key)

def update_test():
    '''更新数据...'''
    print('更新数据...')
    sh = get_shelve()
    update(sh)

def deleteall_test():
    '''删除所有数据'''
    print('删除所有数据...')
    sh = get_shelve()
    deleteall(sh)
    
###############################################################
###                测试           END
###############################################################

def init():
    global SHOW_LOG
    SHOW_LOG = True
    print('SHOW_LOG : {}'.format(SHOW_LOG))
    deleteall_test()
    save_test()

def main():
    init()
    print('#' * 50)
    fetchall_test()
    print('#' * 50)
    update_test()
    print('#' * 50)
    fetchall_test()
    print('#' * 50)
    fetchone_test()
    print('#' * 50)
    delete_test()
    print('#' * 50)
    fetchall_test()
    print('#' * 50)
    deleteall_test()
    print('#' * 50)
    fetchall_test()
    
if __name__ == '__main__':
    main()

```

# re
重点介绍re模块

## re.match
re.match 尝试`从字符串的开始匹配`一个模式，如：下面的例子匹配第一个单词。

```python
>>> import re
>>> text = "JGood is a handsome boy, he is cool, clever, and so on..."
>>> m = re.match(r"(\w+)\s", text)
>>> if m:
...     print m.group(0), '\n', m.group(1)
... else:    
...     print 'not match'
... 
JGood  
JGood

```
## re.search
re.search函数会在字符串内查找模式匹配,只到找到第一个匹配然后返回，如果字符串没有匹配，则返回None。
```python
>>> text = "JGood is a handsome boy, he is cool, clever, and so on..."
>>> m = re.search(r'\shan(ds)ome\s', text)
>>> if m:
...     print m.group(0), m.group(1)
... else:    
...     print 'not match'
... 
 handsome  ds

```
`re.match与re.search的区别`：re.match只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None；而re.search匹配整个字符串，直到找到一个匹配。

## re.sub
re.sub用于替换字符串中的匹配项。下面一个例子将字符串中的空格 ' ' 替换成 '-' :
```python

>>> import re
>>> text = "JGood is a handsome boy, he is cool, clever, and so on..."
>>> print re.sub(r'\s+', '-', text) 
JGood-is-a-handsome-boy,-he-is-cool,-clever,-and-so-on...

```
## re.split
可以使用re.split来分割字符串
```python
>>> re.split(r'\s+', text)
['JGood', 'is', 'a', 'handsome', 'boy,', 'he', 'is', 'cool,', 'clever,', 'and', 'so', 'on...']
```

## re.findall
re.findall可以获取字符串中所有匹配的字符串。

```python
>>> re.findall(r'\w*oo\w*', text)
['JGood', 'cool']
```
## re.compile
可以把正则表达式编译成一个正则表达式对象。可以把那些经常使用的正则表达式编译成正则表达式对象，这样可以提高一定的效率。
```python

>>> import re
>>> text = "JGood is a handsome boy, he is cool, clever, and so on..."
>>> regex = re.compile(r'\w*oo\w*')
>>> print regex.findall(text)   #查找所有包含'oo'的单词
['JGood', 'cool']

>>> print regex.sub(lambda m: '[' + m.group(0) + ']', text) #将字符串中含有'oo'的单词用[]括起来。
[JGood] is a handsome boy, he is [cool], clever, and so on...
```
# fileinput
用fileinput 对文件进行循环遍历，格式化输出，查找、替换等操作，非常方便.

fileinput模块可以对一个或多个文件中的内容进行迭代、遍历等操作。
该模块的input()函数有点类似文件readlines()方法，区别在于前者是一个迭代对象，需要用for循环迭代，后者是一次性读取所有行。

## 模块说明
```
fileinput.input (files=None, inplace=False, backup='', bufsize=0, mode='r', openhook=None)
files:                  #文件的路径列表，默认是stdin方式，多文件['1.txt','2.txt',...]
inplace:                #是否将标准输出的结果写回文件，默认不取代
backup:                 #备份文件的扩展名，只指定扩展名，如.bak。如果该文件的备份文件已存在，则会自动覆盖。
bufsize:                #缓冲区大小，默认为0，如果文件很大，可以修改此参数，一般默认即可
mode:                   #读写模式，默认为只读
openhook:               #该钩子用于控制打开的所有文件，比如说编码方式等;
```

## 常用函数

```
fileinput.input()       #返回能够用于for循环遍历的对象
fileinput.filename()    #返回当前文件的名称
fileinput.lineno()      #返回当前已经读取的行的数量（或者序号）
fileinput.filelineno()  #返回当前读取的行的行号
fileinput.isfirstline() #检查当前行是否是文件的第一行
fileinput.isstdin()     #判断最后一行是否从stdin中读取
fileinput.close()       #关闭队列
```
## 实例
利用fileinput及re做分析: 提取符合条件的电话号码
```python
#---样本文件: phone.txt---
010-110-12345
800-333-1234
010-99999999
05718888888
021-88888888
#---测试脚本: test.py---
import re
import fileinput
pattern = '[010|021]-\d{8}'  #提取区号为010或021电话号码，格式:010-12345678
for line in fileinput.input('phone.txt'):
  if re.search(pattern,line):
    print '=' * 50
    print 'Filename:'+ fileinput.filename()+' | Line Number:'+str(fileinput.lineno())+' | '+line,
#---输出结果:---
>>> 
==================================================
Filename:phone.txt | Line Number:3 | 010-99999999
==================================================
Filename:phone.txt | Line Number:5 | 021-88888888
>>>
```