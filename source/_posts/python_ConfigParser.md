title: ConfigParser模块处理INI配置文件
date: 2015-11-18 13:50:02
tags:
 - ConfigParser
categories: [python]

---
# ConfigParser模块中方法介绍
**基本的读取配置文件**
- read(filename) 直接读取ini文件内容
- sections() 得到所有的section，并以列表的形式返回
- options(section) 得到该section的所有option
- items(section) 得到该section的所有键值对
- get(section,option) 得到section中option的值，返回为string类型
- getint(section,option) 得到section中option的值，返回为int类型，还有相应的getboolean()和getfloat() 函数。
<!--more -->

**基本的写入配置文件**
-add_section(section) 添加一个新的section
-set( section, option, value) 对section中的option进行设置，需要调用write将内容写入配置文件。

# ini格式文件
Python ConfigParser模块解析的配置文件的格式比较象ini的配置文件格式，就是文件中由多个section构成，每个section下又有多个配置项，比如：  
db_config.ini
```python
[baseconf]
host=127.0.0.1
port=3306
user=root
password=root
db_name=evaluting_sys
[concurrent]
processor=20
```
# 简单应用
```python
#!/usr/bin/python
#-*- coding: utf-8 -*-

import sys,os
import ConfigParser

filedir = "/export/leicx/sa_dev/python/config/"
filename = "db_config.ini"
file = os.path.join(filedir,filename)

def test(config_file_path):
    cf = ConfigParser.ConfigParser()
    cf.read(config_file_path)
    # 返回所有的section
    s = cf.sections()
    print 'section:', s

    o = cf.options("baseconf")
    print 'options:', o

    v = cf.items("baseconf")
    print 'db:', v
    #可以按照类型读取出来
    db_host = cf.get("baseconf", "host")
    db_port = cf.getint("baseconf", "port")
    db_user = cf.get("baseconf", "user")
    db_pwd = cf.get("baseconf", "password")

    print db_host, db_port, db_user, db_pwd
	  #修改一个值，再写回去
    cf.set("baseconf", "password", "654321")
    cf.write(open(config_file_path, "w"))


    #添加一个section。（同样要写回）  
    cf.add_section('vike')
    cf.set('vike', 'int', '15')
    cf.set('vike', 'bool', 'true')
    cf.set('vike', 'float', '3.1415')
    cf.set('vike', 'baz', 'fun')
    cf.set('vike', 'bar', 'Python')
    cf.set('vike', 'foo', '%(bar)s is %(baz)s!')
    cf.write(open(config_file_path, "w"))

    #移除section 或者option 。（只要进行了修改就要写回的哦）  
    #cf.remove_option('vike','int')  
    #cf.remove_section('vike')  
    #cf.write(open(config_file_path, "w")) 

if __name__ == "__main__":
    test(file)

```
上边的方式还是不够通用，可以改成类的方式写个通用模块，通过参数的方式获取更灵活。
通用模块代码如下

```python
#!/usr/bin/python
#-*- coding: utf-8 -*-

import sys,os,time
import ConfigParser

class Config:
    def __init__(self, path):
        self.path = path
        self.cf = ConfigParser.ConfigParser()
        self.cf.read(self.path)
    def get(self, field, key):
        result = ""
        try:
            result = self.cf.get(field, key)
        except:
            result = ""
        return result
    def set(self, filed, key, value):
        try:
            self.cf.set(field, key, value)
            cf.write(open(self.path,'w'))
        except:
            return False
        return True

def read_config(config_file_path, field, key): 
    cf = ConfigParser.ConfigParser()
    try:
        cf.read(config_file_path)
        result = cf.get(field, key)
    except:
        sys.exit(1)
    return result

def write_config(config_file_path, field, key, value):
    cf = ConfigParser.ConfigParser()
    try:
        cf.read(config_file_path)
        cf.set(field, key, value)
        cf.write(open(config_file_path,'w'))
    except:
        sys.exit(1)
    return True

if __name__ == "__main__":
   if len(sys.argv) < 4:
      sys.exit(1)

   config_file_path = sys.argv[1] 
   field = sys.argv[2]
   key = sys.argv[3]
   if len(sys.argv) == 4:
      print read_config(config_file_path, field, key)
   else:
      value = sys.argv[4]
      write_config(config_file_path, field, key, value)

```



