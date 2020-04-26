title: 多网卡绑定
date: 2016-04-05 17:30:35
tags:
 - linux
categories: [linux]

---

## 多网卡绑定Bonding生产实战

#### 一、什么是网卡绑定及简单原理
   网卡绑定也称作"网卡捆绑"，就是使用多块物理网卡虚拟成为一块网卡，以提供负载均衡或者冗余，增加带宽的作用。当一个网卡坏掉时，不会影响业务。这个聚合起来的设备看起来是一个单独的以太网接口设备，也就是这几块网卡具有相同的IP地址而并行链接聚合成一个逻辑链路工作。这种技术在Cisco等网络公司中，被称为Trunking和Etherchannel 技术，在Linux的2.4.x的内核中把这种技术称为bonding。
   
<!--more -->   
#### 二、技术分类
1. 负载均衡
   对于bonding的网络负载均衡是我们在文件服务器中常用到的，比如把三块网卡，当做一块来用，解决一个IP地址，流量过大，服务器网络压力过大的问题。 对于文件服务器来说，比如NFS或SAMBA文件服务器，没有任何一个管理员会把内部网的文件服务器的IP地址弄很多个来解决网络负载的问题。如果在内网中，文件服务器为了管理和应用上的方便，大多是用同一个IP地址。对于一个百M的本地网络来说，文件服务器在多 个用户同时使用的情况下，网络压力是极大的，特别是SAMABA和NFS服务器。为了解决同一个IP地址，突破流量的限制，毕竟网线和网卡对数据的吞吐量是有限制的。如果在有限的资源的情况下，实现网络负载均衡，最好的办法就是 bonding。
2. 网络冗余
   对于服务器来说，网络设备的稳定也是比较重要的，特别是网卡。在生产型的系统中，网卡的可靠性就更为重要了。在生产型的系统中，大多通过硬件设备的冗余来提供服务器的可靠性和安全性，比如电源。bonding 也能为网卡提供冗余的支持。把多块网卡绑定到一个IP地址，当一块网卡发生物理性损坏的情况下，另一块网卡自动启用，并提供正常的服务，即：默认情况下只有一块网卡工作，其它网卡做备份。
   
#### 三、bonding负载均衡和网络冗余技术的现实

1. 网络环境
```
[root@web-31 ~]# cat /etc/issue
CentOS release 6.3 (Final)
Kernel \r on an \m
[root@web-31 ~]# getconf LONG_BIT
64
[root@web-31 ~]# ip a |grep -v lo
2: em1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
   link/ether f0:4d:a2:3e:57:00 brd ff:ff:ff:ff:ff:ff
   inet 172.28.2.31/24 brd 172.28.2.255 scope global em1
3: em2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
   link/ether f0:4d:a2:3e:57:02 brd ff:ff:ff:ff:ff:ff
4: em3: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
   link/ether f0:4d:a2:3e:57:04 brd ff:ff:ff:ff:ff:ff
5: em4: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
   link/ether f0:4d:a2:3e:57:06 brd ff:ff:ff:ff:ff:ff
PS : 使用四块网卡进行bonding绑定：em1、em2、em3、em4
```
2. 检查bonding环境
有如下方法检测：
```
[root@web-31 ~]# modinfo bonding |grep bonding.ko
filename:       /lib/modules/2.6.32-279.5.2.el6.centos.plus.x86_64/kernel/drivers/net/bonding/bonding.ko
[root@web-31 ~]# modprobe -l bond*
kernel/drivers/net/bonding/bonding.ko
```
3. 加载bonding模块
```
[root@web-31 ~]# lsmod | grep 'bonding'
[root@web-31 ~]# modprobe bonding
WARNING: Deprecated config file /etc/modprobe.conf, all config files belong into /etc/modprobe.d/.
PS: 加载模块时，发出警告，意思是：当前内核版本已经弃用配置文件/etc/modprobe.conf，所有的配置文件属于/etc/modprobe.d，也就是说，以后的加载模块要写入到配置文件时要写到/etc/modprobe.conf这个配置文件中！因此，这里bonding的模块配置文件也要独立一个配置文件！
[root@web-31 ~]# lsmod | grep 'bonding'
bonding               127806  0
ipv6                  322637  167 bonding
8021q                  25941  1 bonding
开机自动加载模块到内核：
echo 'modprobe bonding &> /dev/null' >> /etc/rc.local
```
4. 创建虚拟网卡配置文件
```
cd /etc/sysconfig/network-scripts
cat >> ifcfg-bond0 << EOF
DEVICE=bond0
ONBOOT=yes
BOOTPROTO=static
IPADDR=172.28.2.31
BROADCAST=172.28.2.255
NETMASK=255.255.255.0
GATEWAY=172.28.2.1
USERCTL=no
TYPE=Ethernet
EOF
```
5. 修改真实网卡配置文件
```
mv ifcfg-em{1,2,3,4} /tmp/bakcup
em1配置文件内容：
cat >> ifcfg-em1 << EOF
DEVICE="em1"
ONBOOT="yes"
BOOTPROTO="none"
USERCTL="no"
MASTER="bond0"
SLAVE="yes"
EOF
em2配置文件内容：
cat >> ifcfg-em2 << EOF
DEVICE="em2"
ONBOOT="yes"
BOOTPROTO="none"
USERCTL="no"
MASTER="bond0"
SLAVE="yes"
EOF
em3配置文件内容：
cat >> ifcfg-em3 << EOF
DEVICE="em3"
ONBOOT="yes"
BOOTPROTO="none"
USERCTL="no"
MASTER="bond0"
SLAVE="yes"
EOF
em4配置文件内容：
cat >> ifcfg-em4 << EOF
DEVICE="em4"
ONBOOT="yes"
BOOTPROTO="none"
USERCTL="no"
MASTER="bond0"
SLAVE="yes"
EOF
```
6. 编辑模块载入配置文件，让系统支持bonding
```
cat >> /etc/modprobe.d/bonding.conf <<EOF
#/etc/modprobe.conf
alias bond0 bonding
options bond0 miimon=100 mode=4 lacp_rate=1
EOF
```
PS :
>a. mode=4 这里我采用这种模式，总共有0，1，2，3，4，5，6七种模式，文章后面会详细介绍这七种模式！
此模式下所以网卡共用一个MAC地址，后面结果会有验证！
b. miimon是用来进行链路监测的，如:miimon=100，那么系统每100ms监测一次链路连接状态，如果有一条线路不通就转入另一条线路；
c. bonding只能提供链路监测，即从主机到交换机的链路是否接通。如果只是交换机对外的链路down掉了，而交换机本身并没有故障，那么bonding会认为链路没有问题而继续使用。

7. 重启服务器或重启网络进行测试
```
[root@web-31 ~]# service network restart
[root@web-31 ~]# ip a |grep -v lo
2: em1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master bond0 state UP qlen 1000
   link/ether f0:4d:a2:3e:57:00 brd ff:ff:ff:ff:ff:ff
3: em2: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master bond0 state UP qlen 1000
   link/ether f0:4d:a2:3e:57:00 brd ff:ff:ff:ff:ff:ff
4: em3: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master bond0 state UP qlen 1000
   link/ether f0:4d:a2:3e:57:00 brd ff:ff:ff:ff:ff:ff
5: em4: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master bond0 state UP qlen 1000
   link/ether f0:4d:a2:3e:57:00 brd ff:ff:ff:ff:ff:ff
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
   link/ether f0:4d:a2:3e:57:00 brd ff:ff:ff:ff:ff:ff
   inet 172.28.2.38/24 brd 172.28.2.255 scope global bond0
   inet6 fe80::f24d:a2ff:fe3f:23c0/64 scope link
      valid_lft forever preferred_lft forever
[root@liuhe038 ~]#
```
PS: 生产服务器下，测试略！mode=4这种模式要配置交换机相应端口开启链路聚合！
#### 四、对于mode七种模式的简单介绍

>mode=0 : 可实现网络负载均衡和网络冗余，采用平衡轮循策略(balance-rr)。
此模式的特点：
a. 所以网卡都工作，传输数据包顺序是依次传输，也就是说第1个包经过eth0，下一个包就经过eth1，一直循环下去，直到最后一个包传输完毕。
b. 此模式对于同一连接从不同的接口发出的包，中途传输过程中再经过不同的链接，在客户端很有可能会出现数据包无序到达的问题，而无序到达的数据包需要重新要求被发送，这样网络的吞吐量就会下降。
c. 与网卡相连的交换必须做特殊配置（这两个端口应该采取聚合方式），因为做bonding的这两块网卡是使用同一个MAC地址

>mode=1：只实现网络冗余，采用主-备份策略(active/backup)。
此模式的特点：
a. 此模式下只有一块网卡正常工作，另外的网卡都是备份用状态，当另外一块网卡坏掉后另一块网卡马上由备份转换为主。
b. 此模式好处是可以提供高网络连接的可用性，但是它的资源利用率较低，只有一个接口处于工作状态。

>mode=2：可模式提供负载平衡和容错能力，采用平衡策略(balance-xor)。
此模式的特点：基于指定的传输HASH策略传输数据包，这种模式用得相对少！

>mode=3：只提供容错能力，采用广播策略。
此模式特点：在每个slave接口上传输每个数据包。

>mode=4：IEEE 802.3ad 动态链接聚合，使用lagp协议，可以带宽翻倍增加！
此模式的特点：
创建一个聚合组，它们共享同样的速率和双工设定。根据802.3ad规范将多个slave工作在同一个激活的聚合体下。
外出流量的slave选举是基于传输hash策略，该策略可以通过xmit_hash_policy选项从缺省的XOR策略改变到其他策略。
用此模式必要条件：
条件1：ethtool支持获取每个slave的速率和双工设定
条件2：switch(交换机)支持IEEE 802.3ad Dynamic link aggregation
条件3：大多数switch(交换机)需要经过特定配置才能支持802.3ad模式

>mode=5：采用适配器传输负载均衡(balance-tlb)
此模式特点：
特点：不需要任何特别的switch(交换机)支持的通道bonding。在每个slave上根据当前的负载（根据速度计算）分配外出流量。如果正在接受数据的slave出故障了，另一个slave接管失败的slave的MAC地址。
该模式的必要条件：ethtool支持获取每个slave的速率

>mode=6：可实现网络负载均衡和网络冗余,采用load balancing (round-robin)方式。
此模式的特点：
a. 两块网卡都处于工作状态，但各网卡的MAC地址是不一样
b. 该模式下无需配置交换机，因为做bonding的这两块网卡是使用不同的MAC地址。
这种模式是所最复杂的，不再详细介绍！

#### 五、总结
生产中常用的是第 0，1，6，4这几中模式
如果实现简单的负载均衡可以采用 mode=0或mode=6（注意区别mode=0和mode=6两种模式的区别）
如果实现简单的网络冗余可以采用 mode=1（此模式可以用VM虚拟机模拟）