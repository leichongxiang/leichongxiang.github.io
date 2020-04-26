title: ipmitool
date: 2016-05-04 17:30:35
tags:
 - linux
categories: [linux]

---

## 1.简介
 IPMI是智能型平台管理接口（Intelligent Platform Management Interface）的缩写，用户可以利用IPMI监视服务器的物理健康特征，如温度、电压、风扇工作状态、电源状态等。
Ipmi 最大的优势在于它是独立于 CPU BIOS 和 OS 的，所以用户无论在开机还是关机的状态下，只要接通电源就可以实现对服务器的监控。Ipmi 是一种规范的标准，其中最重要的物理部件就是BMC(Baseboard Management Controller) 一种嵌入式管理微控制器，它相当于整个平台管理的“大脑”，通过它 ipmi 可以监控各个传感器的数据并记录各种事件的日志。

在工作时，所有的IPMI功能都是向BMC发送命令来完成的，命令使用IPMI规范中规定的指令，BMC接收并在系统事件日志中记录事件消息，维护描述系统中传感器情况的传感器数据记录。

当需要对系统 文本控制台进行远程访问时，Serial Over LAN (SOL) 功能将非常有用。SOL 通过 IPMI 会话重定向本地串行接口，允许远程访问 Windows 的紧急事件管理控制台 (EMS) 特殊管理控制台 (SAC)，或访问 LINUX 串行控制台。这个过程的步骤是 IPMI 固件截取数据，然后通过局域网重新发送定向到串行端口的信息。 这就提供了远程查看 BOOT、OS 加载器或紧急事件管理控制台以诊断并修复服务器相关问题的标准方法，而无需考虑供应商。它允许在引导阶段配置各种组件。

![bmc](http://7xnmpy.com1.z0.glb.clouddn.com/blog/imge/bmc.png)

<!--more -->

## 2.服务器硬件本身提供对 ipmi 的支持 （硬件）
    目前惠普、戴尔和 NEC 等大多数厂商的服务器都支持 IPMI 2.0，但并不是所有服务器都支持，所以应该先通过产品手册或在 BIOS 中确定服务器是否支持 ipmi，也就是说服务器在主板上要具有 BMC 等嵌入式的管理微控制器。

这里拿DELL R710为例:
1) 启动服务器 使用ctrl+e 进去ipmi server mangement configuration

2)设置IPMI Over LAN 为On
![R710](http://7xnmpy.com1.z0.glb.clouddn.com/blog/imge/01.png)

3)进入IPMI Parameters 设置服务器ip/子网掩码 (也可以进去系统通过Ipmitool管理软件设置)
![ipmi-2](http://7xnmpy.com1.z0.glb.clouddn.com/blog/imge/02.jpg)

4）进入LAN User Confuguration 设置用户名 密码 （同样也可以进去系统通过Ipmitool管理软件设置）
![ipmi-3](http://7xnmpy.com1.z0.glb.clouddn.com/blog/imge/03.jpg)

安装ipmi：
```bash
yum install ipmi
或者源码
wget http://jaist.dl.sourceforge.net/project/ipmitool/ipmitool/1.8.17/ipmitool-1.8.17.tar.bz2

启动
service ipmi start
```
	
	
## 3.常用的管理命令包括：

### 3.1.系统管理命令

1. 查看设备信息
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin chassis status`

2. 查看用户
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user list`

3. 增加用户
```bash
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user set name 3 test1
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user list
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user set password 3 test1
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user priv 3 20
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user list
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U test1 -P test1 user list
```
4. disable/enable用户
```bash
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user disable 3
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U test1 -P test1 user list
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin user enable 3
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U test1 -P test1 user list
```
5. 查看服务器当前开电状态
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin power status`

6. 服务器的开机，关机，reset和power cycle
```bash
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin power on
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin power off
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin power cycle
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin power reset
```
7. 查看服务器的80 Port当前状态
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin raw 0x30 0xB2`

8. 查看服务器的传感器状态
```bash
所有传感器状态详细信息：
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sensor
传感器SDR summary信息：
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sdr info
传感器SDR 列表信息：
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sdr list
FRU传感器SDR 列表信息：
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sdr list fru
下载RAW SDR信息到文件：
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sdr dump sdr.raw
```
9. 查看服务器的FRU信息
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin fru
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin fru print
```

### 3.2.BMC自身配置命令

1. 查看BMC的信息
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin mc info`

2. 查看BMC的LAN信息
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin lan print 1`
（一般channel 1为LAN）

3. 修改BMC的MAC信息（只能在本地以root用户做，因为在此之前没IP）
```
enable BMC MAC SET mode:
/usr/bin/ipmitool raw 0x0c 0x01 0x01 0xc2 0x00
Write MAC to BMC (BMC MAC=d0:27:88:a4:e4:37):
/usr/bin/ipmitool raw 0x0c 0x01 0x01 0x05 0xD0 0x27 0x88 0xA4 0xE4 0x37
```
4. 修改BMC的网络为自动从DHCP获得IP地址，而不是静态的（只能在本地以root用户做，因为在此之前没IP）
```
确定channel 1为LAN:
/usr/bin/ipmitool lan print 1
设定channel 1从DHCP获得IP:
/usr/bin/ipmitool lan set 1 ipsrc dhcp
```
5. 重启BMC自己（不是服务器）（小心BMC挂掉hang）
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin mc reset`


### 3.3.SOL和通过IPMItool访问系统终端 (Serial-Over-LAN)

1. 查看当前的SOL summary信息
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sol info 1`

2. 修改SOL配置信息
```
查看所有可能的配置
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sol set
修改波特率配置
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sol set non-volatile-bit-rate 38.4 1
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sol set volatile-bit-rate 38.4 1
```
3. 开启远程终端
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sol activate
(可以使用~.退出,~?显示帮助信息)
```

### 3.4.Watchdog配置命令

1. 查看当前的watchdog信息
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin mc watchdog get`

2. 设置，开启一个watchdog
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin mc watchdog get
设置一个OS WDT的watchdog, 超时时间为60秒
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin raw 0x06 0x24 0x04 0x01 0x00 0x10 0x58 0x2
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin mc watchdog get
开启该watchdog
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin mc watchdog reset
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin mc watchdog get

禁止该watchdog的动作(Hard reset-> no action)

/usr/bin/ipmitool -I lanplus -H 10.32.228.187 -U sysadmin -P admin raw 0x06 0x24 0x04 0x00 0x00 0x10 0x58 0xFF

上面的命令把时间改为非常大，提示第1个0x00表示没有动作，0x04表示是SMS/OS的watchdog, 0xFF58是超时的时间，单位为100ms。
```

### 3.5.SEL命令

1. 查看当前的SEL summary信息
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel info
```
2. 列示所有SEL记录详细信息
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel list
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel list 10
```
3. 删除指定的SEL记录
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel delete 1`

4. 清除所有的SEL记录
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel clear`

5. 获取和修改SEL当前时钟
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel time get
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin sel time set "04/24/2012 18:44:44"
```
6. 以RAW方式查看制定的SEL数据
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin raw 0xa 0x43 0 0 111 0 0 0xFF

0xa 0x43为Get SEL Entry Command； 0 0 保留值，111 0 表示取第112条记录（从0开始），0 为offset，保留；0xFF为读取的字节数，FF表示取整条记录
```

### 3.6.PEF命令

1. 查看BMC当前的PEF 支持信息
```
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin pef
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin pef info
```
2. 查看BMC当前的PEF 配置表信息（配置表也是可以修改的）
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin pef list`

3. 查看BMC当前的PEF 状态信息(BMC处理的最后一条SEL记录)
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin pef status`

4. 修改BMC当前的PEF 配置表
```
查看当前的PEF 配置表
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin pef list
假定我们要删除下面这条配置项
1 | active, pre-configured | 0x11 | Voltage | Any | None | OEM | Any | Power-off,OEM-defined
获取该配置项的配置信息
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin raw 0x04 0x13 0x07 0x01 0x00
11 01 40
修改该配置项的配置信息
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin raw 0x04 0x12 0x07 0x01 0x40
检查修改后的PEF配置表
/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin pef list
```
 
### 3.7.特殊命令

1. 查看ipmi服务器端当前活动的session会话
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin session info active`

2. 执行一个保存在文件中的所有ipmitool命令
`/usr/bin/ipmitool -I lanplus -H 10.88.1.181 -U sysadmin -P admin exec myipmi.cmd`
	
