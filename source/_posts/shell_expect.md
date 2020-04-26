title: shell中调用expect
date: 2016-03-02 21:53:35
tags:
 - shell
categories: [linux]
photos:
 - http://ww2.sinaimg.cn/large/8e712944jw1f1k0oq9ovmj20go0nlwev.jpg
---

### 说明
在批量部署手游环境的时候，经常需要批量对组内的服务器做信任，所以可以利用shell中的expect去自动化。

### 实例
#### 实例1：使用expect来完成密码应答
```
#!/bin/bash
auto_login_ssh() {
    expect -c "set timeout -1;
                spawn -noecho ssh -o StrictHostKeyChecking=no $2 ${@:3};
                expect *assword:*;
                send -- $1\r;
                interact;";
}
 
auto_login_ssh passwd user@host
```
StrictHostKeyChecking=no参数让ssh默认添加新主机的公钥指纹，也就不会出现出现是否继续yes/no的提示了。

<!--more -->
#### 实例2：
使用expect后，程序的exit status是expect的，而不是ssh的，所以如果遇上连接不到的主机、密码错误等情况，expect也一样是正常退出，$?为0，所以需要对expect的代码稍加修改：
```
#!/bin/bash
auto_smart_ssh () {
    expect -c "set timeout -1;
                spawn ssh -o StrictHostKeyChecking=no $2 ${@:3};
                expect {
                    *assword:* {send -- $1\r;
                                 expect {
                                    *denied* {exit 2;}
                                    eof
                                 }
                    }
                    eof         {exit 1;}
                }
                "
    return $?
}
 
auto_smart_ssh passwd user@host ls /var
echo -e "\n---Exit Status: $?"
```
#### 实例3：
使用ssh-copy-id实现自动添加key文件
```
passwd='nihao'
login='"-p 30022 root@'
host="$1\""
auto_ssh_copy_id()
{
    expect -c "set timeout -1;
                spawn ssh-copy-id ${login}${host};
                expect {
                    *(yes/no)* {send -- yes\r;exp_continue;}
                    *assword:* {send -- $passwd\r;exp_continue;}
                    eof        {exit 0;}
                }";
}
```