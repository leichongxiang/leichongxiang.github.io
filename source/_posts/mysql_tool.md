title: mysql工具
date: 2016-01-26 11:30:35
tags:
 - mysql工具
categories: [mysql]

---

# 简单sql
```bash
创建数据库
create database name; 
选择数据库
use databasename; 
直接删除数据库，不提醒
drop databasename; 
在shell中执行，删除数据库前，有提示。
mysqladmin drop database name 
显示表
show tables; 
表的详细描述
describe tablename; 

select 中加上distinct去除重复字段

显示当前mysql版本和当前日期和当前用户
 select version(),current_date,current_user();
```
注意
如下脚本创建数据库yourdbname，并制定默认的字符集是utf8。
`CREATE DATABASE IF NOT EXISTS yourdbname DEFAULT CHARSET utf8 COLLATE utf8_general_ci;`

如果要创建默认gbk字符集的数据库可以用下面的sql:
`create database yourdb DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci;`
<!--more -->

# mysql客户端连接
## mysql在连接的时候，可以设置字符集去连接
```
[root@templet ~]# mysql -uroot -p
mysql> show variables where variable_name like 'chara%_client';
+----------------------+--------+
| Variable_name        | Value  |
+----------------------+--------+
| character_set_client | latin1 |
+----------------------+--------+

[root@templet ~]# mysql -uroot -p --default-character-set=utf8
mysql> show variables where variable_name like 'chara%_client';
+----------------------+-------+
| Variable_name        | Value |
+----------------------+-------+
| character_set_client | utf8  |
+----------------------+-------+

```
## shell中直接执行mysql语句用-e
```
[root@templet ~]# mysql -uroot -p -e "select user,host from mysql.user"
+------+-----------+
| user | host      |
+------+-----------+
| root | 127.0.0.1 |
| root | ::1       |
| root | localhost |
+------+-----------+
```
## 格式化选项
-E，--vertical 将输出按照字母顺序竖着排列
-s, --silent 去掉mysql中的框线
```
[root@templet ~]# mysql -uroot -p -e "select user,host from mysql.user" -E
或者
[root@templet ~]# mysql -uroot -p -E 
 *************************** 1. row ***************************
user: root
host: 127.0.0.1
*************************** 2. row ***************************
user: root
host: ::1
*************************** 3. row ***************************
user: root
host: localhost


[root@templet ~]# mysql -uroot -p -s
Enter password: 
mysql>  select user,host from mysql.user;
user    host
root    127.0.0.1
root    ::1
root    localhost
mysql> 

```
## 错误处理
-f --force 		强制执行sql
-v --verbose	显示更多信息
--show-warnings 显示警告信息

在批量执行sql中，如果其中一个错误，正常情况下是，该批处理会停止退出。加上-f，则会跳过出差的sql，强制执行后边的sql；-v显示错误的sql语句；--show-warnings,显示全部错误告警信息，一般会一起使用

# mysql管理工具
* mysqladmin是一个用于执行管理性操作，检查服务器的配置和当前的状态、创建并删除数据库等。
* mysqlbinlog程序的主要功能就是分析MySQLServer所产生的二进制日志
* mysqlcheck客户端可以检查和修复MyISAM表。它还可以优化和分析表。 
* mysqldump
* mysqlhotcopy只能备份MyISAM，而且运行在linux/unix环境中
* mysqlimport数据导入工具
* perror错误代码查看工具
*safe_mysqld 一个脚本，用某些更安全的特征启动mysqld守护进程，例如当一个错误发生时，重启服务器并且记载运行时刻信息到一个日志文件中。


mysqlbinlog程序的主要功能就是分析MySQLServer所产生的二进制日志。当我们希望通过之前备份的binlog做一些指定时间之类的恢复的时候，mysqlbinlog就可以帮助我们找到恢复操作需要做哪些事情。通过mysqlbinlog，我们可以解析出binlog中指定时间段或者指定日志起始和结束位的内容解析成SQL 语句，并导出到指定的文件中，在解析过程中，还可以通过指定数据库名称来过滤输出内容
```
show binary logs;    #显示binlog文件
purge binary logsto 'mysql-bin.**'  #删除到**文件
bin/mysqlbinlog binlogfile    #解析binlog文件
 
利用binlog恢复数据：
bin/mysqlbinlog  --start-datetime='2011-7-7 18:0:0'--stop-datetime='2011-7-7 20:07:13' data/mysql-bin.000008|mysql -u root
```


mysqlcheck的功能类似myisamchk，但其工作不同。主要差别是当mysqld服务器在运行时必须使用mysqlcheck，而myisamchk应用于服务器没有运行时。使用mysqlcheck的好处是不需要停止服务器来检查或修复表。使用myisamchk修复失败是不可逆的。
```
shell> mysqlcheck[options] db_name [tables] 
shell> mysqlcheck[options] ---database DB1 [DB2 DB3...] 
shell> mysqlcheck[options] --all--database

-a = Analyse given tables. 
-c = Check table for errors 
-o = Optimise table 
-r = Can fix almost anything except unique keys that aren't unique 
-m = --medium-check
```

mysqlimport用来导入，mysqldump加-T之后导出的文本文件。和load data datainfile子句类似。
```
1. 数据文件的位置

load data的文件位置：
    如果为绝对路径，则直接访问绝对路径下的文件。
    如果为相对路径（只有文件名），则在默认数据库的数据目录中寻找文件。
    如果为相对路径（包含文件夹），则在数据库数据目录下寻找相应文件夹。

load data local的文件位置：
    如果为绝对路径，则直接访问绝对路径下的文件。
    如果为相对路径，则在当前目录下寻找。


2. local的用法
mysqlimport -L database 'name.txt'
mysql> load data local infile 'name.txt' into table talbe_name


3. 指定文件格式
fields-terminated-by 
fields-enclosed-by
fields-escaped-by
lines-terminated-by
可用转义符，也可用ASCII码
\t = 0x09
\n = 0x0a
\r = 0x0d

4. 不同操作系统的行结束符
Unix : \n
Windows: \r\n

5. 处理重复的值
load data local infile 'name.txt' replace into table table_name
或者使用mysqlimport的--replace选项

6. 跳过文件头几行
load data infile 'name.txt' into table table_name ignore 1 lines;

7. 修改文件数据和列值的对应关系
load data infile 'name.txt' into table table_name (col1,col3,col2);

8. 插入前对数据进行预处理
load data infile 'name.txt' into table table_name (@col1,@col2,@col3)
set 
col1 = substr(@col1,2),
col2 = concat(@col2,'_suffix'),
col3 = @col3 *10;

9. 忽略某一列
load data infile 'name.txt' into table table_name (col1,col2,@dummy,col3);

10. 用mysqldump导出文件
mysql> select * from group_map into outfile "/tmp/addresslist2.txt" fields terminated by "," enclosed by "'" ;

fields terminated 为字段終止符
enclosed 为字段封装符
结果：
'a0001','e100','咨询中心'

load data infile ''/tmp/addresslist2.txt'' into table group_map fields terminated by '','' enclosed by ''''';

mysqldump -T /tmp --fields-terminated-by=, --fields-enclosed-by=\' adeptechsync emp
adeptechsync库中的emp表导出到/tmp/emp.txt,字段之间是用,來分隔,没个字段本身是为'封装的.
mysqlimport --fields-terminated-by=, --fields-enclosed-by=\' adeptechsync /tmp/emp.txt

perror
[root@templet ~]# perror 30
OS error code  30:  Read-only file system
```