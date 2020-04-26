title: expect
date: 2015-11-23 14:50:02
tags:
 - expect
 - shell
categories: [shell]

---
shell脚本需要交互的地方可以使用here文档是实现，但是有些命令却需要用户手动去就交互如passwd、scp。

对自动部署免去用户交互很痛苦，expect能很好的解决这类问题。
<!--more -->

# expect的核心
expect的核心是spawn expect send set

spawn 调用要执行的命令
expect 等待命令提示信息的出现，也就是捕捉用户输入的提示：
send 发送需要交互的值，替代了用户手动输入内容
set 设置变量值
interact 执行完成后保持交互状态，把控制权交给控制台，这个时候就可以手工操作了。如果没有这一句登录完成后会退出，而不是留在远程终端上。
expect eof 这个一定要加的，与spawn对应表示捕获终端输出信息终止，类似于if....endif

expect脚本必须以interact或expect eof结束，执行自动化任务通常expect eof就够了。

设置expect永不超时
set timeout -1

设置expect 300秒超时，如果超过300没有expect内容出现，则推出
set timeout 300

expect编写语法，expect使用的是tcl语法。

一条Tcl命令由空格分割的单词组成. 其中, 第一个单词是命令名称, 其余的是命令参数
cmd arg arg arg

$符号代表变量的值. 在本例中, 变量名称是foo.


方括号执行了一个嵌套命令. 例如, 如果你想传递一个命令的结果作为另外一个命令的参数, 那么你使用这个符号
[cmd arg]

双引号把词组标记为命令的一个参数. "$"符号和方括号在双引号内仍被解释
"some stuff"

大括号也把词组标记为命令的一个参数. 但是, 其他符号在大括号内不被解释
{some stuff}

反斜线符号是用来引用特殊符号. 例如：n 代表换行. 反斜线符号也被用来关闭"$"符号, 引号,方括号和大括号的特殊含义

扩展名：exp

日志记录： 在expect脚本中添加`log_file a.txt`就会将日志记录在a.txt中，默认也会在终端中显示，如果不需要显示可以./test.exp >/dev/null


# expect简单例子
```bash
#!/usr/bin/expect     
set timeout 30     
spawn ssh -l username 192.168.1.1     
expect "password:"     
send "ispass\r"     
interact 
```


# expect传参数

[lindex $argv 0]表示脚本的第0个参数

完成对服务器的scp任务
```bash
#!/usr/bin/expect  
set timeout 10  
set host [lindex $argv 0]  
set username [lindex $argv 1]  
set password [lindex $argv 2]  
set src_file [lindex $argv 3]  
set dest_file [lindex $argv 4]  
spawn scp $src_file $username@$host:$dest_file  
 expect {  
 "(yes/no)?"  
   {  
    send "yes\n"  
    expect "*assword:" { send "$password\n"}  
 }  
 "*assword:"  
{  
 send "$password\n"  
}  
}  
expect "100%"  
expect eof  
```

# expect嵌套在shell中

```bash
#!/bin/sh  
list_file=$1  
src_file=$2  
dest_file=$3  
cat $list_file | while read line  
do  
   host_ip=`echo $line | awk '{print $1}'`  
   username=`echo $line | awk '{print $2}'`  
   password=`echo $line | awk '{print $3}'`  
   echo "$host_ip"  
   ./expect_scp $host_ip $username $password $src_file $dest_file  
done   
```
# 自动化脚本建立主机之间的SSH信任关系
```bash
#!/usr/bin/bash  
#usage ./ssh_trust.sh host1 user1 passwd1 host2 user2 passwd2  
#即建立从user1@host1到user2@host2的ssh信任。  
src_host=$1  
src_username=$2  
src_passwd=$3  
  
dst_host=$4  
dst_username=$5  
dst_passwd=$6  
  
#在远程主机1上生成公私钥对  
Keygen()  
{  
expect << EOF  
spawn ssh $src_username@$src_host ssh-keygen -t rsa  
while 1 {  
        expect {  
                 "password:" {  
                              send "$src_passwd\n"  
                               }  
                "yes/no*" {  
                            send "yes\n"  
                          }  
                        "Enter file in which to save the key*" {  
                                        send "\n"  
                        }  
                        "Enter passphrase*" {  
                                        send "\n"  
                        }  
                        "Enter same passphrase again:" {  
                                        send "\n"  
                                        }  
  
                        "Overwrite (y/n)" {  
                                        send "n\n"  
                        }  
                        eof {  
                                   exit  
                        }  
        }  
}  
EOF  
}  
#从远程主机1获取公钥保存到本地  
Get_pub()  
{  
expect << EOF  
spawn scp $src_username@$src_host:~/.ssh/id_rsa.pub /tmp  
expect {  
             "password:" {  
                           send "$src_passwd\n";exp_continue  
                }  
                "yes/no*" {  
                           send "yes\n";exp_continue  
                }     
                eof {  
                                exit  
                }  
}  
EOF  
}  
#将公钥的内容附加到远程主机2的authorized_keys  
Put_pub()  
{  
src_pub="$(cat /tmp/id_rsa.pub)"  
expect << EOF  
spawn ssh $dst_username@$dst_host "mkdir -p ~/.ssh;echo $src_pub >> ~/.ssh/authorized_keys;chmod 600 ~/.ssh/authorized_keys"  
expect {  
            "password:" {  
                        send "$dst_passwd\n";exp_continue  
             }  
            "yes/no*" {  
                        send "yes\n";exp_continue  
             }     
            eof {  
                        exit  
             }   
}  
EOF  
}  
Keygen  
Get_pub  
Put_pub 
```
