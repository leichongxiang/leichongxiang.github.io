title: ss命令替代netstat
date: 2016-04-14 14:30:35
tags:
 - linux
categories: [linux]

---

## ss命令

#### 说明
ss命令用来显示处于活动状态的套接字信息。ss命令可以用来获取socket统计信息，它可以显示和netstat类似的内容。但ss的优势在于它能够显示更多更详细的有关TCP和连接状态的信息，而且比netstat更快速更高效。 当服务器的socket连接数量变得非常大时，无论是使用netstat命令还是直接cat /proc/net/tcp，执行速度都会很慢。可能你不会有切身的感受，但请相信我，当服务器维持的连接达到上万个的时候，使用netstat等于浪费 生命，而用ss才是节省时间。 天下武功唯快不破。ss快的秘诀在于，它利用到了TCP协议栈中tcp_diag。tcp_diag是一个用于分析统计的模块，可以获得Linux 内核中第一手的信息，这就确保了ss的快捷高效。当然，如果你的系统中没有tcp_diag，ss也可以正常运行，只是效率会变得稍慢。

```
netstat -at | wc  耗时 15.60 秒
ss -atr     | wc  耗时  5.40 秒（未利用tcp_diag）
ss -atr     | wc  耗时  0.47 秒（利用tcp_diag）
```
   
<!--more -->  
#### 安装
```
yum install iproute iproute-doc
```
从某种意义上说，iproute工具集几乎可以替代掉net-tools工具集，具体的替代方案是这样的：
```
用途			net-tool（被淘汰）		iproute2
地址和链路配置	ifconfig				ip addr, ip link
路由表			route					ip route
邻居			arp						ip neigh
VLAN			vconfig					ip link
隧道			iptunnel				ip tunnel
组播			ipmaddr					ip maddr
统计			netstat					ss
```

#### 实例
##### 场景一：我想查看当前服务器的网络连接统计
```
$ ss -s
Total: 295 (kernel 312)
TCP:   48 (estab 1, closed 31, orphaned 0, synrecv 0, timewait 0/0), ports 13

Transport Total     IP        IPv6
*         312       -         -
RAW       0         0         0
UDP       2         2         0
TCP       17        12        5
INET      19        14        5
FRAG      0         0         0
```

常用ss命令
```
ss列出每个进程名及其监听的端口
# ss -pl
1

ss列所有的tcp sockets
# ss -t -a
1

ss列出所有udp sockets
# ss -u -a
1

ss列出所有http连接中的连接
# ss -o state established '( dport = :http or sport = :http )'
1
以上包含对外提供的80，以及访问外部的80
用以上命令完美的替代netstat获取http并发连接数，监控中常用到

ss列出本地哪个进程连接到x server
# ss -x src /tmp/.X11-unix/*
1

ss列出处在FIN-WAIT-1状态的http、https连接
# ss -o state fin-wait-1 '( sport = :http or sport = :https )'
1

```

ss使用IP地址筛选
```
ss src ADDRESS_PATTERN
src：表示来源
ADDRESS_PATTERN：表示地址规则

如下：
ss src 120.33.31.1 # 列出来之20.33.31.1的连接

＃　列出来至120.33.31.1,80端口的连接
ss src 120.33.31.1:http
ss src 120.33.31.1:80

ss src ADDRESS_PATTERN
src：表示来源
ADDRESS_PATTERN：表示地址规则
 
如下：
ss src 120.33.31.1 # 列出来之20.33.31.1的连接
```
ss使用端口筛选
```
ss dport OP PORT
OP:是运算符
PORT：表示端口
dport：表示过滤目标端口、相反的有sport
```
OP运算符如下：
```
<= or le : 小于等于 >= or ge : 大于等于
== or eq : 等于
!= or ne : 不等于端口
< or lt : 小于这个端口 > or gt : 大于端口
```
OP实例
```
ss sport = :http 也可以是 ss sport = :80
ss dport = :http
ss dport \> :1024
ss sport \> :1024
ss sport \< :32000
ss sport eq :22
ss dport != :22
ss state connected sport = :http
ss \( sport = :http or sport = :https \)
ss -o state fin-wait-1 \( sport = :http or sport = :https \) dst 192.168.1/24
```

