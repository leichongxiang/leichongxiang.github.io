title: shell脚本中问题01
date: 2015-10-29 17:53:35
tags:
 - linux
 - shell
categories: [linux]

---

# <font color="red">问题1：shell的位置参数\$1与每行的第一个字段\$1冲突</font>

比如awk读取shell的位置参数\$1 ,如果直接放在awk命令中会与每行的第一个字段\$1冲突,需要特殊处理。
```bash
#!/bin/bash
echo 3 | awk  '{if ($1==3) print $1 "不是" "'$1'"}'

sh a.sh 7
3不是7
```
实例2：
```bash
#!/bin/bash

SOCK_FILE=`ssh -p 30022 $1 "cat /etc/my.cnf | grep sock | head -1|awk -F'=' '{print "'$2'" }' " `
echo $SOCK_FILE

[root@manager ~]# sh a.sh  backup
/tmp/mysql3306.sock
```
<!-- more -->

# <font color="blue">处理方法：</font>
用单引号隔离shell的变量，并且用双引号包围在shell变量的两边。`"'$1'"`
或者
用双引号隔离shell的变量，并且用单引号包围在shell变量的两边。`'"$1"'`
```bash
#!/bin/bash
echo 3 | awk  '{if ($1==3) print $1 "不是" '"$1"'}'

[root@manager ~]# sh  b.sh 7
3不是7
```
<font color="red">引申抽象出来的问题1：**从shell中向awk传递变量的方法：**</font>

awk使用shell变量
## <font color="blue">方法1：使用-v选项</font>
```bash
#!/bin/bash

var="test"
awk -v nvar="$var" 'BEGIN{print nvar}'
```

## <font color="blue">方法2："'var'" </font>
"'var'" 双引号单引号 变量 单引号 双引号
```bash
#!/bin/bash

var="test"
awk 'BEGIN{print "'$var'"}' #第一种
awk 'BEGIN{ print "'"$var"'" }' #第二种
```
这种写法要求变量var中不含有空格。若var中含有空格，那么就要用 “‘“$var”’”
## <font color="blue">方法3：export变量，然后用ENVIRON［“var”］</font>
```bash
#!/bin/bash

var="test"
export var
awk 'BEGIN{print ENVIRON["var"]}'
```

# <font color="red">引申抽象出来的问题2：**从awk中向shell传递变量的方法：eval**</font>
shell使用awk传递出来的变量
```bash
along@along-laptop:~/code/shell/shell$ cat awktest.sh
#!/bin/bash
var1="test"
var2="along"

eval $(awk 'BEGIN{print "var1=along;var2=test"}')
echo "var1:"$var1
echo "var2:"$var2
along@along-laptop:~/code/shell/shell$ ./awktest.sh
var1:along
var2:test
```




# <font color="red">问题2：使用sed..| while read line 读取ip列表，只读取第一个ip 后就退出脚本的情况</font>

【背景】
工作过程中遇到要从一个ip列表中获取ip port，然后ssh ip 到目标机器进行特定的操作，但是编写脚本的过程 使用while read line 读取ip列表，在while循环中只读取第一个ip 后就退出脚本的情况。

```bash
#!/bin/bash
IPS="10.1.1.10 3003
10.1.1.11 3001"
echo "====while test ===="
i=0

echo $IPS | while read line
do
    echo $(($i+1))
    echo $line
done

echo "====for test ===="
n=0
for ip in $IPS ;
do
   n=$(($n+1))
   echo $ip
   echo $n
done

输出结果如下

====while test ====
1
10.1.1.10 3003 10.1.1.11 3001
====for test ====

10.1.1.10
1
3003
2
10.1.1.11
3
3001
4
```

由例子可见 while read line 是一次性将信息读入并赋值给line ，而for是每次读取一个以空格为分割符的字符串。
**<font color="blue">原因：</font>**
while中使用重定向机制,IPS中的所有信息都被读入并重定向给了整个while 语句中的line 变量。所以当我们在while循环中再一次调用read语句，就会读取到下一条记录。问题就出在这里，$line中的最后一行已经读完，无法获取下一行记录，从而退出 while循环。

**<font color="blue">处理方法：</font>**
1 使用ssh -n "command"
2 ssh "cmd" < /dev/null 将ssh 的输入重定向输入。
