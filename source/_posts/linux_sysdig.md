title: sysdig命令
date: 2016-05-09 16:30:35
tags:
 - sysdig
categories: [linux]

---

## 说明
<div style="border-radius:10px; width: 90%; border:10px solid #AFEEEE;font-size:16px;align=left;  background:#AFEEEE">当你需要追踪某个进程产生和接收的系统调用时，首先浮现在你脑海中的是什么？你可能会想到strace，那么你是对的。你会使用什么样的命令行工具来监控原始网络通信呢？如果你想到了tcpdump，你又作出了一个极佳的选择。而如果你碰到必须追踪打开的文件（在Unix意义上：一切皆文件）的需求，可能你会使用lsof。strace+tcpdump+lsof=sysdig。</div>

<!-- more -->
strace、tcpdump以及lsof，确实是些伟大的工具，它们应该成为每个系统管理员工具集之中的一部分，而这也正是你为什么应该爱上sysdig的原因。它是一个强大的开源工具，用于系统级别的勘察和排障，它的创建者在介绍它时称之为“strace+tcpdump+lsof+上面点缀着lua樱桃的绝妙酱汁”。抛开幽默不说，sysdig的最棒特性之一在于，它不仅能分析Linux系统的“现场”状态，也能将该状态保存为转储文件以供离线检查。更重要的是，你可以自定义sysdig的行为，或者甚至通过内建的（你也可以自己编写）名为凿子（chisel）的小脚本增强其功能。单独的凿子可以以脚本指定的各种风格分析sysdig捕获的事件流。

----------

## Sysdig Examples

Note: Make sure you also take a look at csysdig, which packs a lot of useful functionality into a simple to use UI.
Note: The command lines on this page return live data. However, you can use them with trace files too by just adding the -r switch.
Note: If you need a list of basic sysdig commands, for instance to learn how to create a trace file, see the quick reference guide

Networking
Containers
Application
Disk I/O
Processes and CPU usage
Performance and Errors
Security

### Networking

#### See the top processes in terms of network bandwidth usage
```bash
sysdig -c topprocs_net
```
Show the network data exchanged with the host 192.168.0.1
As binary:
```bash
sysdig -s2000 -X -c echo_fds fd.cip=192.168.0.1  
```
As ASCII:
```bash
sysdig -s2000 -A -c echo_fds fd.cip=192.168.0.1
```
#### See the top local server ports

In terms of established connections:

`sysdig -c fdcount_by fd.sport "evt.type=accept"  `
In terms of total bytes:

`sysdig -c fdbytes_by fd.sport`
#### See the top client IPs

In terms of established connections

`sysdig -c fdcount_by fd.cip "evt.type=accept"  `
In terms of total bytes

`sysdig -c fdbytes_by fd.cip`
List all the incoming connections that are not served by apache.

`sysdig -p"%proc.name %fd.name" "evt.type=accept and proc.name!=httpd"`

### Containers

View the list of containers running on the machine and their resource usage

`sudo csysdig -vcontainers`
View the list of processes with container context

`sudo csysdig -pc`
View the CPU usage of the processes running inside the wordpress1 container

`sudo sysdig -pc -c topprocs_cpu container.name=wordpress1`
View the network bandwidth usage of the processes running inside the wordpress1 container

`sudo sysdig -pc -c topprocs_net container.name=wordpress1`
View the processes using most network bandwidth inside the wordpress1 container

`sudo sysdig -pc -c topprocs_net container.name=wordpress1`
View the top files in terms of I/O bytes inside the wordpress1 container

`sudo sysdig -pc -c topfiles_bytes container.name=wordpress1`
View the top network connections inside the wordpress1 container

`sudo sysdig -pc -c topconns container.name=wordpress1`
Show all the interactive commands executed inside the wordpress1 container

`sudo sysdig -pc -c spy_users container.name=wordpress1`

### Application

See all the GET HTTP requests made by the machine

`sudo sysdig -s 2000 -A -c echo_fds fd.port=80 and evt.buffer contains GET`
See all the SQL select queries made by the machine

`sudo sysdig -s 2000 -A -c echo_fds evt.buffer contains SELECT `
See queries made via apache to an external MySQL server happening in real time

`sysdig -s 2000 -A -c echo_fds fd.sip=192.168.30.5 and proc.name=apache2 and evt.buffer contains SELECT`

### Disk I/O

See the top processes in terms of disk bandwidth usage

`sysdig -c topprocs_file`
List the processes that are using a high number of files

`sysdig -c fdcount_by proc.name "fd.type=file"`
See the top files in terms of read+write bytes

`sysdig -c topfiles_bytes`
Print the top files that apache has been reading from or writing to

`sysdig -c topfiles_bytes proc.name=httpd`
Basic opensnoop: snoop file opens as they occur

`sysdig -p "%12user.name %6proc.pid %12proc.name %3fd.num %fd.typechar %fd.name" evt.type=open`
See the top directories in terms of R+W disk activity

`sysdig -c fdbytes_by fd.directory "fd.type=file"`
See the top files in terms of R+W disk activity in the /tmp directory

`sysdig -c fdbytes_by fd.filename "fd.directory=/tmp/"`
Observe the I/O activity on all the files named 'passwd'

`sysdig -A -c echo_fds "fd.filename=passwd"`
Display I/O activity by FD type

`sysdig -c fdbytes_by fd.type`

### Processes and CPU usage

See the top processes in terms of CPU usage

`sysdig -c topprocs_cpu`
Observe the standard output of a process

`sysdig -s4096 -A -c stdout proc.name=cat`

### Performance and Errors

See the files where most time has been spent

`sysdig -c topfiles_time`
See the files where apache spent most time

`sysdig -c topfiles_time proc.name=httpd`
See the top processes in terms of I/O errors

`sysdig -c topprocs_errors`
See the top files in terms of I/O errors

`sysdig -c topfiles_errors`
See all the failed disk I/O calls

`sysdig fd.type=file and evt.failed=true`
See all the failed file opens by httpd

`sysdig "proc.name=httpd and evt.type=open and evt.failed=true"`
See the system calls where most time has been spent

`sysdig -c topscalls_time`
See the top system calls returning errors

`sysdig -c topscalls "evt.failed=true"`
snoop failed file opens as they occur

`sysdig -p "%12user.name %6proc.pid %12proc.name %3fd.num %fd.typechar %fd.name" evt.type=open and evt.failed=true`
Print the file I/O calls that have a latency greater than 1ms:

`sysdig -c fileslower 1`

### Security

Show the directories that the user "root" visits

`sysdig -p"%evt.arg.path" "evt.type=chdir and user.name=root"`
Observe ssh activity

`sysdig -A -c echo_fds fd.name=/dev/ptmx and proc.name=sshd`
Show every file open that happens in /etc

`sysdig evt.type=open and fd.name contains /etc`
Show the ID of all the login shells that have launched the "tar" command

`sysdig -r file.scap -c list_login_shells tar`
Show all the commands executed by the login shell with the given ID

`sysdig -r trace.scap.gz -c spy_users proc.loginshellid=5459`