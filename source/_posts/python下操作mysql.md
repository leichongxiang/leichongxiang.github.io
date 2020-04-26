title: python操作mysql
date: 2015-11-17 14:50:02
tags:
 - python
 - MySQLdb使用
categories: [python]

---

操作MySQL数据库的首选模块MySQLdb。
基本步骤：
- 引入MySQLdb库
- 和数据库建立连接
- 执行sql语句和接收返回值
- 关闭游标关闭数据库连接 

<!--more -->

# 数据库连接
`conn=MySQLdb.connect(host="localhost",user="root",passwd="sa",db="mytable",charset="utf8") `

# 游标对象方法
cursor=conn.cursor()  用使用连接对象获得一个cursor对象
n=cursor.execute(sql,param)
使用cursor提供的方法来进行工作.这些方法包括两大类:
1.执行命令
2.接收返回值

cursor用来执行命令的方法:
callproc(self, procname, args):用来执行存储过程,接收的参数为存储过程名和参数列表,返回值为受影响的行数 
execute(self, query, args):执行单条sql语句,接收的参数为sql语句本身和使用的参数列表,返回值为受影响的行数 
executemany(self, query, args):执行单条sql语句,但是重复执行参数列表里的参数,返回值为受影响的行数 
nextset(self):移动到下一个结果集 

cursor用来接收返回值的方法: 
fetchall(self):接收全部的返回结果行. 
fetchmany(self, size=None):接收size条返回结果行.如果size的值大于返回的结果行的数量,则会返回cursor.arraysize条数据. 
fetchone(self):返回一条结果行. 
scroll(self, value, mode='relative'):移动指针到某一行.如果mode='relative',则表示从当前所在行移动value条,如果mode='absolute',则表示从结果集的第一行移动value条.  

# 关闭游标和数据库
需要分别的关闭指针对象和连接对象.他们有名字相同的方法 
cursor.close() 
conn.close()

# 实例
```python
# -*- coding: utf-8 -*-     
#mysqldb    
import time, MySQLdb    
   
#连接    
conn=MySQLdb.connect(host="localhost",user="root",passwd="",db="test",charset="utf8")  
cursor = conn.cursor()    
   
#写入    
sql = "insert into user(name,created) values(%s,%s)"   
param = ("aaa",int(time.time()))    
n = cursor.execute(sql,param)    
print n    
   
#更新    
sql = "update user set name=%s where id=3"   
param = ("bbb")    
n = cursor.execute(sql,param)    
print n    
   
#查询    
n = cursor.execute("select * from user")    
for row in cursor.fetchall():    
    for r in row:    
        print r    
   
#删除    
sql = "delete from user where name=%s"   
param =("aaa")    
n = cursor.execute(sql,param)    
print n    
cursor.close()    
   
#关闭    
conn.close()   
```

一次性返回所有的数据未必可行，我们可以取逐行依次取回。
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

import MySQLdb as mdb
import sys


con = mdb.connect('localhost', 'testuser', 
    'test623', 'testdb');

with con:

    cur = con.cursor()
    cur.execute("SELECT * FROM Writers")
    #确认SQL语句查询结果中包含的行数
    numrows = int(cur.rowcount)
    #使用fetchone()方法逐行获取数据
    for i in range(numrows):
        row = cur.fetchone()
        print row[0], row[1]

```