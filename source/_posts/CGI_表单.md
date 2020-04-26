title: python写CGI脚本
date: 2015-11-15 14:50:02
tags:
 - CGI
categories: [python]

---

一直以来都想要实现从浏览器点击页面调用服务器上的脚本，实现简单的自动化运维功能。

# CGI是什么
 CGI是common gateway interface 的简称。通用网关接口（Common Gateway Interface/CGI）是一种重要的互联网技术，可以让一个客户端，从网页浏览器向执行在网络服务器上的程序请求数据。CGI描述了客户端和服务器程序之间传输数据的一种标准。`CGI程序可以是Python脚本，PERL脚本，SHELL脚本，C或者C++程序等`。
<!--more -->

CGI的传值有两种方法，一种是GET，GET的主要途径和表现就是他的数据是通过地址传输的，也就是地址栏输入的地址，然后在服务器端读取$QUERY_STRING变量的值就可以了，GET方法很简单，但是GET方法的致命弱点是不适合于大数据的传输，因为浏览器地址是有长度限制的，这种情况下，我们就需要使用POST方法了；另一种是POST方法，POST方法的传值方法是通过读取标准输入完成的。
 
输出的第一行必须是输出“Content-type: text/html“类似的语句，否则Apache识别不了这个文本页面的输出。

 
# Web服务器支持与配置
在进行CGI编程之前，请确保Web服务器支持CGI，它被配置为处理CGI程序。所有对由HTTP服务器执行的CGI程序保存在一个预先配置的目录。此目录被称为CGI目录，并按照惯例被命名为/var/www/cgi-bin目录。按照惯例，CGI文件具有扩展名为.cgi，但文件扩展名可以为Python语言脚本 .py。

默认情况下，Linux服务器被配置为只运行在在/var/www/cgi-bin目录中的脚本。如果想指定要运行的CGI脚本任何其他目录，在httpd.conf文件中注释以下几行：
```bash
<Directory "/var/www/cgi-bin">
   AllowOverride None
   Options ExecCGI
   Order allow,deny
   Allow from all
</Directory>

<Directory "/var/www/cgi-bin">
Options All
</Directory>
```

 
 # python编写表单
 运行CGI程序之前，请确保有使用chmod 755 hello.py UNIX命令，使文件可执行文件的更改模式。
 
 hello.html文件
 ```html
 <!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Hello Get</title>
</head>

<form action="/cgi-bin/hello_get.py" method="get">
First Name: <input type="text" name="first_name">  <br />

Last Name: <input type="text" name="last_name" />
<input type="submit" value="Submit" />
</form>
</html>
 
 ```
 hello_get.py文件内容
 ```python
 #!/usr/bin/python
# -*- coding: UTF-8 -*-

# CGI处理模块
import cgi, cgitb

# 创建 FieldStorage 的实例化
form = cgi.FieldStorage()

# 获取数据
first_name = form.getvalue('first_name')
last_name  = form.getvalue('last_name')

print "Content-type:text/html\r\n\r\n"
print "<html>"
print "<head>"
print "<title>Hello - Second CGI Program</title>"
print "</head>"
print "<body>"
print "<h2>Hello %s %s</h2>" % (first_name, last_name)
print "</body>"
print "</html>"
 ```

# GET方法和POST方法
当需要从浏览器中传递一些信息到Web服务器中的CGI程序。最常见的是浏览器会使用两种方法二传这个信息给Web服务器。这两个方法分别是GET方法和POST方法。

## 使用GET方法传递信息：
GET方法将附加在页面请求所编码的用户信息。页面和编码信息是由 ？字符分开如下：

/cgi-bin/hello_get.py?first_name=ZARA&last_name=ALI
GET方法是默认的方法，从浏览器的信息传递到Web服务器和它所产生出现在浏览器的位置，如果是很长的字符串，或如果有密码或其他敏感信息传递给服务器，切勿使用GET方法。 GET方法有大小限制：仅1024个字符可以在请求字符串被发送。 GET方法将使用QUERY_STRING头信息，并会通过QUERY_STRING环境变量的CGI程序访问。

可以通过简单的串联键和值对传递以及任何URL信息，或者可以使用HTML<form>标记使用GET方法来传递信息。
第一个例子就是get方法传递信息的

## 使用POST方法传递信息
将信息传递给CGI程序的一般比较可靠的方法是POST方法。这个包中完全相同的方式作为GET方法的信息，但是，代替发送它作为后一个文本字符串？在URL中，它把它作为一个单独的消息。此消息进入在标准输入表单的CGI脚本。
```html
<form action="/cgi-bin/hello_get.py" method="post">
First Name: <input type="text" name="first_name"><br />
Last Name: <input type="text" name="last_name" />

<input type="submit" value="Submit" />
</form>
```
hello_get.py代码
```python
#!/usr/bin/python

# Import modules for CGI handling 
import cgi, cgitb 

# Create instance of FieldStorage 
form = cgi.FieldStorage() 

# Get data from fields
first_name = form.getvalue('first_name')
last_name  = form.getvalue('last_name')

print "Content-type:text/html

"
print "<html>"
print "<head>"
print "<title>Hello - Second CGI Program</title>"
print "</head>"
print "<body>"
print "<h2>Hello %s %s</h2>" % (first_name, last_name)
print "</body>"
print "</html>"
```


# 使用shell写cgi脚本
先来一个hello world试试
## 简单的实例
在apache的cgi-bin目录下，如/var/www/cgi-bin/目录下写入helloworld.sh，内容如下：
```bash
#!/bin/bash
echo "Content-type: text/html"
echo ""
echo "Hello World"
```
`注意必须有echo "Content-type: text/html"`
给helloworld.sh赋予可执行权限，然后访问http://ip/cgi-bin/helloworld.sh就可以看到shell cgi执行的效果了。

## shell接受传递的参数
这里提供一个已经完成这种功能的脚本—proccgi.sh（文件在网上可以搜到）。这个脚本可以直接解析出get和post方法传过来的数据。

使用方法：

在CGI的shell脚本中调用eval `./proccgi.sh $*`语句，然后使用“$FORM_”和参数的key，就可以获得参数的值了，如task_id="$FORM_taskid"。
```bash
#!/bin/bash

eval `./proccgi.sh $*`
echo "Content-type: text/html"
echo 
task_id="$FORM_taskid"
print $task_id

```

一个最简单的shell的CGI程序了，从前台将taskid=XXXX，传给CGI程序。CGI中调用了proccgi.sh脚本，将taskid的值解析出来存放到变量FORM_taskid中。这样，就已经拿到taskid的值了。接下来print task_id，是将task_id的值输出到标准输出，到此CGI程序就全部结束了，CGI会将print到标准输出的内容全部当作http的response，返回给浏览器。