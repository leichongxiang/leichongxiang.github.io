title: Python的CSV模块 
date: 2015-11-19 14:50:02
tags:
 - CSV
categories: [python]

---

很多时候我们都需要将服务器信息收集起来整理成csv，或者execl格式的表格，呈现给别人。所以就有了这样的需求，如何用python操作csv文件。
python的csv模块，简单易用。
<!--more -->

# 读取
`reader(csvfile[, dialect='excel'][, fmtparam])`
参数表:

csvfile
        需要是支持迭代(Iterator)的对象，并且每次调用next方法的返回值是字符串(string)，通常的文件(file)对象，或者列表(list)对象都是适用的，如果是文件对象，打开是需要加"b"标志参数。

dialect
        编码风格，默认为excel方式，也就是逗号(,)分隔，另外csv模块也支持excel-tab风格，也就是制表符(tab)分隔。其它的方式需要自己定义，然后可以调用register_dialect方法来注册，以及list_dialects方法来查询已注册的所有编码风格列表。

fmtparam
        格式化参数，用来覆盖之前dialect对象指定的编码风格。


```python
import csv

reader = csv.reader(file('your.csv', 'rb'))
for line in reader:
    print line

```

# 写入
`writer(csvfile[, dialect='excel'][, fmtparam])`
参数表(略: 同reader, 见上)
```python
import csv

writer = csv.writer(file('your.csv', 'wb'))
writer.writerow(['Column1', 'Column2', 'Column3'])
lines = [range(3) for i in range(5)]
for line in lines:
    writer.writerow(line)
```


	
	