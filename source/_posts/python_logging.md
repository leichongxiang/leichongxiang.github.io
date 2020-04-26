title: logging模块
date: 2015-11-14 13:50:02
tags:
 - logging
categories: [python]

---
# logging模块中方法介绍
使用logging模块记录日志涉及四个主要类，使用官方文档中的概括最为合适：
* logger提供了应用程序可以直接使用的接口；
* handler将(logger创建的)日志记录发送到合适的目的输出；
* filter提供了细度设备来决定输出哪条日志记录；
* formatter决定日志记录的最终输出格式。

<!--more -->
# 简单的将日志打印到屏幕
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-
import logging
 
logging.debug('This is debug message')
logging.info('This is info message')
logging.warning('This is warning message')

#结果
WARNING:root:This is warning message
```
默认情况下，logging将日志打印到屏幕，日志级别为WARNING；
日志级别大小关系为：CRITICAL > ERROR > WARNING > INFO > DEBUG > NOTSET，当然也可以自己定义日志级别。

# 将日志信息同时输出到文件和屏幕
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

import logging

logging.basicConfig(level=logging.DEBUG,
                format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
                datefmt='%a, %d %b %Y %H:%M:%S',
                filename='myapp.log',
                filemode='w')

#--------------------------------------------------
# 日志格式
#--------------------------------------------------
# %(asctime)s       年-月-日 时-分-秒,毫秒 2013-04-26 20:10:43,745
# %(filename)s      文件名，不含目录
# %(pathname)s      目录名，完整路径
# %(funcName)s      函数名
# %(levelname)s     级别名
# %(lineno)d        行号
# %(module)s        模块名
# %(message)s       消息体
# %(name)s          日志模块名
# %(process)d       进程id
# %(processName)s   进程名
# %(thread)d        线程id
# %(threadName)s    线程名
				
#################################################################################################
#定义一个StreamHandler，将INFO级别或更高的日志信息打印到标准错误，并将其添加到当前的日志处理对象#
console = logging.StreamHandler()
console.setLevel(logging.INFO)
formatter = logging.Formatter('%(name)-12s: %(levelname)-8s %(message)s')
console.setFormatter(formatter)
logging.getLogger('').addHandler(console)
#################################################################################################

logging.debug('This is debug message')
logging.info('This is info message')
logging.warning('This is warning message')


#结果
屏幕上打印:

root        : INFO     This is info message
root        : WARNING  This is warning message
./myapp.log文件中内容为:

Sun, 24 May 2009 21:48:54 demo2.py[line:11] DEBUG This is debug message
Sun, 24 May 2009 21:48:54 demo2.py[line:12] INFO This is info message
Sun, 24 May 2009 21:48:54 demo2.py[line:13] WARNING This is warning message

```

# 通过logging.config模块配置日志
```python
#logger.conf
###############################################
[loggers]
keys=root,example01,example02
[logger_root]
level=DEBUG
handlers=hand01,hand02
[logger_example01]
handlers=hand01,hand02
qualname=example01
propagate=0
[logger_example02]
handlers=hand01,hand03
qualname=example02
propagate=0
###############################################
[handlers]
keys=hand01,hand02,hand03
[handler_hand01]
class=StreamHandler
level=INFO
formatter=form02
args=(sys.stderr,)
[handler_hand02]
class=FileHandler
level=DEBUG
formatter=form01
args=('myapp.log', 'a')
[handler_hand03]
class=handlers.RotatingFileHandler
level=INFO
formatter=form02
args=('myapp.log', 'a', 10*1024*1024, 5)
```
在logging中使用配置文件
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

import logging
import logging.config
 
logging.config.fileConfig("logger.conf")
logger = logging.getLogger("example01")
 
logger.debug('This is debug message')
logger.info('This is info message')
logger.warning('This is warning message')
```

