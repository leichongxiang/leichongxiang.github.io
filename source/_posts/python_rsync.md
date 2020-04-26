title: python的rsync应用
date: 2015-12-02 16:53:35
tags:
 - rsync
categories: [python]

---
需要获取rsync备份的实时进度，主要是用到subprocess模块的管道功能。
```python
popen = subprocess.Popen(['ping', 'www.baidu.com', '-n', '3'], stdout = subprocess.PIPE)

while True:
    print popen.stdout.readline()
```
rsync这种软件的进度是在一行里面实时刷新的，显示进度的缓冲区并没有释放，所以用readline就读不出来，还是得等到整个程序执行结束之后才能返回。

<!--more -->

```python
import subprocess
import sys
import time
import random
import os
import re

if __name__ == '__main__':
    cmdLine = []
    cmdLine.append('rsync')
    cmdLine.append('--progress')
    cmdLine.append('/kvmdata/kvm/VM_winxp/winxp.img')
    cmdLine.append('/root/winxp.img')
    tmpFile = "./tmp/%d.tmp" % random.randint(10000,99999)  #临时生成一个文件
    fpWrite = open(tmpFile,'w')
    process = subprocess.Popen(cmdLine,stdout = fpWrite,stderr = subprocess.PIPE);
    while True:
        fpRead = open(tmpFile,'r')  
        lines = fpRead.readlines()
        for line in lines:
            print line
        if process.poll():
            break;
        fpWrite.truncate()  #此处清空文件，等待记录下一次输出的进度
        fpRead.close()
        time.sleep(3)
    fpWrite.close()
    error = process.stderr.read()
    if not error == None:
        print 'error info:%s' % error
    os.popen('rm -rf %s' % tmpFile) #删除临时文件
    print 'finished'
```
这样就可以实时获取到Rsync的备份进度了

