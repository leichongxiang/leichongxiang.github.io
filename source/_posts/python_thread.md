title: python多线程
date: 2015-12-22 12:30:35
tags:
 - python
categories: [python]

---
通过一个脚本的优化，提高脚本的执行速度，以及容错except，跳过错误，继续执行下边的内容。主要学习如何使用多线程的模型。

a.txt文本的内容，
```bash
[root@manager ~]# cat a.txt 
10.87.5.1:22
10.87.5.2:22  
10.87.5.3:22
10.87.5.4:22
10.87.5.5:22
10.87.5.6:22
10.87.5.7:22
10.87.5.8:22
```
<!--more -->

# 实现将ssh的链接过程分开来写
主要是需要一个全局对象global ssh

```python
#!/usr/bin/env python

import os
import time
import threading
import paramiko

def get_host_port(filename):
    with open(filename,'r') as f:
        #print f.readlines()
        info_list = [ i.strip() for i in f.readlines()]
        return info_list

def create_ssh(host, port):
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(host,int(port))
    return ssh

def exec_cmd(ssh, cmd):
    return ssh.exec_command(cmd)

def close_ssh(ssh):
    ssh.close()

info_list = get_host_port('a.txt')
print time.strftime("%Y-%m-%d %H:%M:%S",time.localtime())
for i in info_list:
    host,port = i.split(':')
    print host,port
    try:
        sshd = create_ssh(host,port)
        stdin, stdout, stderr = exec_cmd(sshd,"ifconfig eth0")
        print stdout.read()
    except Exception,e:
        print e
    finally:
        close_ssh(sshd)

print time.strftime("%Y-%m-%d %H:%M:%S",time.localtime())		
```
这个脚本可以按照文本中的顺序，一次执行得到需要的结果，并且可以跳过异常链接不上的服务器列表。
但是如果机器很多几百上千这样就会太慢。那么能不能用多线程呢？

```python
def ssh2(host,port,cmd):
    try:
        sshd = create_ssh(host,port)
        stdin, stdout, stderr = exec_cmd(sshd,"ifconfig eth0")
        print stdout.read()
    except Exception,e:
        print e
    finally:
        close_ssh(sshd)

threads = []
info_list = get_host_port('a.txt')
print time.strftime("%Y-%m-%d %H:%M:%S",time.localtime())
for i in info_list:
    host,port = i.split(':')
    print host,port
    cmd = 'ifconfig eth0'
    a=threading.Thread(target=ssh2,args=(host,port,cmd))
    a.start()
    a.join()
    
print time.strftime("%Y-%m-%d %H:%M:%S",time.localtime())

```