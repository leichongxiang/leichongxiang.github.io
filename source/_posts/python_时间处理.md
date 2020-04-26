title: python的时间处理
date: 2015-12-07 13:53:35
tags:
 - datetime
categories: [python]

---
Python具有良好的时间和日期管理功能。实际上，计算机只会维护一个`挂钟时间(wall clock time)`，这个时间是从某个固定时间起点到现在的时间间隔。
计算机还可以测量CPU实际上运行的时间，也就是`处理器时间(processor clock time)`，以测量计算机性能。当CPU处于闲置状态时，处理器时间会暂停。

<!--more -->
# time包
time包基于C语言的库函数(library functions)。Python的解释器通常是用C编写的，Python的一些函数也会直接调用C语言的库函数。
```python

import time
print(time.time())   # wall clock time, unit: second
print(time.clock())  # processor clock time, unit: second

#结果
1449468720.78478
0.0
```

time.sleep()可以将程序置于休眠状态
time包还定义了struct_time对象。该对象实际上是将挂钟时间转换为年、月、日、时、分、秒……等日期信息，存储在该对象的各个属性中(tm_year, tm_mon, tm_mday...)。
```python
>>> time.gmtime()
time.struct_time(tm_year=2015, tm_mon=12, tm_mday=7, tm_hour=6, tm_min=36, tm_sec=26, tm_wday=0, tm_yday=341, tm_isdst=0)
>>> time.localtime() # 返回struct_time格式的当地时间, 当地时区根据系统环境决定。

>>> time.mktime(time.gmtime())  # 将struct_time格式转换成wall clock time
1449441440.0
```
# datetime包
datetime包是基于time包的一个高级包， 为我们提供了多一层的便利。

datetime可以理解为date和time两个组成部分。date是指年月日构成的日期(相当于日历)，time是指时分秒微秒构成的一天24小时中的具体时间(相当于手表)。你可以将这两个分开管理(datetime.date类，datetime.time类)，也可以将两者合在一起(datetime.datetime类)。

2015-12-07 14:30用datetime表达
```python
import datetime
t = datetime.datetime(2015,12,7,14,30)
print(t)
2015-12-07 14:30:00
```

# 获取当天date
```python

>>> datetime.date.today()
datetime.date(2015, 1, 12)
```
# 获取明天/前N天
明天
```python

>>> datetime.date.today() + datetime.timedelta(days=1)
datetime.date(2015, 1, 13)
```

# 时间运算
datetime包还定义了时间间隔对象(timedelta)。一个时间点(datetime)加上一个时间间隔(timedelta)可以得到一个新的时间点(datetime)。比如今天的上午3点加上5个小时得到今天的上午8点。同理，两个时间点相减会得到一个时间间隔。

```python
>>> import datetime
>>> t = datetime.datetime(2015,12,7,14,30)
>>> t_next = datetime.datetime(2015,12,7,15,30)
>>> delta1 = datetime.timedelta(seconds = 600) 
>>> delta2 = datetime.timedelta(days = 2) 
         
>>> print(t + delta1)
2015-12-07 14:40:00
>>> print(t + delta2)
2015-12-09 14:30:00
>>> print(t_next - delta1) 
2015-12-07 15:20:00
>>> print(t > t_next)
False
```

# datetime对象与字符串转换
datetime <=> string
strftime的f== formatting
```python
>>> import datetime
>>> datetime.datetime.now()
datetime.datetime(2015, 12, 7, 16, 23, 13, 602978)
>>> datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
'2015-12-07 16:24:01'
```

string -> datetime
datetime.strptime(str, format)
strptime的p = parsing

```python
>>> import datetime        
>>> datetime.datetime.strptime("2015-12-07 16:33:01","%Y-%m-%d %H:%M:%S")         
datetime.datetime(2015, 12, 7, 16, 33, 1)
```

datetime <=> timetuple
```python
>>> import datetime
>>> datetime.datetime.now().timetuple()
time.struct_time(tm_year=2015, tm_mon=1, tm_mday=12, tm_hour=23, tm_min=17, tm_sec=59, tm_wday=0, tm_yday=12, tm_isdst=-1)
```



