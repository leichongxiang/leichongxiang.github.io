title: 获取服务器信息
date: 2015-11-26 19:50:02
tags:
 - python应用
categories: [python]

---
使用python通过管理机获取远程节点的信息。注意下边例子中的addword的数据结构，它可以实现往字典里边添加值的效果。get_host函数可以实现从/etc/hosts文件中获取需要远程的主机列表。

<!--more -->

```python
#!/usr/bin/env python

import re
import sys,os
import psutil
import platform

def addword(index,w,n):
    index.setdefault(w,[]).append(n)
	
def get_net(port,hosts):
    dic = {}
    for host in hosts:
        for line in os.popen("ssh -p %s %s '/sbin/ifconfig'" % (port,host)):
            if 'Ether' in line:
                mac = line.split()[4]
                net = line.split()[0].strip()
            elif 'inet addr' in line:
                ip = line.split()[1].split(":")[1]
                addword(dic,host,net+'|'+mac+'|'+ip)
                #dic[host] = net+'|'+mac+'|'+ip+','
                #print "%s|%s|%s," % (net,mac,ip)
            elif 'lo' in line:
                break
    return dic
	
	
def get_host(file):
    h_list =[]
    p = re.compile(r".*(\s+\w+\d+).*")
    with open(file,'r') as fh:
        content = []
        for line  in fh.readlines():
            host = line.strip().split()
            if len(line) > 1:
                if host[1]== 'localhost':
                    continue
                else:
                    h_list.append(host[1])
    return h_list

def main():
    list = get_host('/etc/hosts')
    net  = get_net('22',list)
    for key in net:
        print key + '====>' + str(net[key])


main()		
```

结果：
```python
autest====>['eth0|00:0C:29:70:CA:E9|10.87.5.3', 'eth1|00:0C:29:70:CA:F3|103.23.44.74']
game01====>['eth0|00:0C:29:30:8D:E4|10.87.5.7', 'eth1|00:0C:29:30:8D:EE|103.23.44.78']
game02====>['eth0|00:0C:29:5D:5A:AE|10.87.5.8', 'eth1|00:0C:29:5D:5A:B8|103.23.44.79']
review====>['eth0|00:0C:29:04:2C:95|10.87.5.5', 'eth1|00:0C:29:04:2C:9F|103.23.44.76']
publish====>['eth0|00:0C:29:6F:1E:A6|10.87.5.1', 'eth1|00:0C:29:6F:1E:B0|103.23.44.72']
manager====>['eth0|00:0C:29:DE:D1:77|10.87.5.254', 'eth1|00:0C:29:DE:D1:81|103.23.44.73']
test====>['eth0|00:0C:29:3C:31:F1|10.87.5.4', 'eth1|00:0C:29:3C:31:FB|103.23.44.75']
aulive====>['eth0|00:0C:29:48:DE:70|10.87.5.6', 'eth1|00:0C:29:48:DE:7A|103.23.44.77']
```