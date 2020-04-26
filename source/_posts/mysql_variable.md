title: mysql的变量查看
date: 2016-01-25 14:30:35
tags:
 - mysql变量
categories: [mysql]

---

# MySQL的变量分为以下两种：
- 系统变量：配置MySQL服务器的运行环境，可以用show variables查看
- 状态变量：监控MySQL服务器的运行状态，可以用show status查看

# 系统变量
系统变量按其作用域的不同可以分为以下两种：
- 分为全局（GLOBAL）级：对整个MySQL服务器有效
- 会话（SESSION或LOCAL）级：只影响当前会话
有些变量同时拥有以上两个级别，MySQL将在建立连接时用全局级变量初始化会话级变量，但一旦连接建立之后，全局级变量的改变不会影响到会话级变量。

<!--more -->

## 查看系统变量的值
** 可以通过show vairables语句查看系统变量的值：**
```
mysql> show variables like 'log%';  
+---------------------------------+-------------------------+
| Variable_name                   | Value                   |
+---------------------------------+-------------------------+
| log                             | OFF                     |
| log_bin                         | OFF                     |
| log_bin_trust_function_creators | OFF                     |
| log_bin_trust_routine_creators  | OFF                     |
| log_error                       | /data/mysql/templet.err |
| log_output                      | FILE                    |
| log_queries_not_using_indexes   | OFF                     |
| log_slave_updates               | OFF                     |
| log_slow_queries                | ON                      |
| log_warnings                    | 1                       |
+---------------------------------+-------------------------+
10 rows in set (0.00 sec)

mysql> show variables  where variable_name like 'log%' and value='ON';   
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| log_slow_queries | ON    |
+------------------+-------+

```
注:show variables优先显示会话级变量的值,如果这个值不存在，则显示全局级变量的值,当然你也可以加上GLOBAL或SESSION关键字区别：
```
show global variables;  
show session/local variables;
```
在写一些存储过程时，可能需要引用系统变量的值，可以使用如下方法：
```
@@GLOBAL.var_name  
@@SESSION.var_name 
或  
@@LOCAL.var_name  
```
如果在变量名前没有级别限定符，将优先显示会话级的值。
** 从INFORMATION_SCHEMA数据库里查看 **
查看变量值的方法是从INFORMATION_SCHEMA数据库里的GLOBAL_VARIABLES和SESSION_VARIABLES表获得。

## 设置和修改系统变量的值
在MySQL服务器启动时，有以下两种方法设置系统变量的值：
- 命令行参数，如：mysqld --max_connections=200
- 选项文件（my.cnf）
在MySQL服务器启动后，如果需要修改系统变量的值，可以通过SET语句：
```bash 
SET GLOBAL var_name = value;  
SET @@GLOBAL.var_name = value;  
SET SESSION var_name = value;  
SET @@SESSION.var_name = value;  
```
如果在变量名前没有级别限定符，表示修改会话级变量。
注意：和启动时不一样的是，在运行时设置的变量不允许使用后缀字母'K'、‘M'等，但可以用表达式来达到相同的效果，如：
`SET GLOBAL read_buffer_size = 2*1024*1024 ` 
 
# 状态变量
状态变量可以使我们及时了解MySQL服务器的运行状况，可以使用show status语句查看。
状态变量和相同变量类似，也分为全局级和会话级，show status也支持like匹配查询，比较大的不同是状态变量只能由MySQL服务器本身设置和修改，对于用户来说是只读的，不可以通过SET语句设置和修改它们.

# 实例
```
-- 设置或修改系统日志有效期
SET GLOBAL expire_logs_days=8;
SHOW VARIABLES LIKE '%expire_logs_days%';


-- 设置或修改系统最大连接数
SET GLOBAL max_connections = 2648;
SHOW VARIABLES LIKE '%max_connections%';


-- 修改MYSQL自动编号步长
SHOW VARIABLES LIKE '%auto_increment%';
SET GLOBAL auto_increment_offset = 1;
SET GLOBAL auto_increment_increment = 1;


比如设置MySQL实例参数wait_timeout为10秒.
 
1) 设置全局变量方法1(不推荐): 修改参数文件, 然后重启mysqld
# vi /etc/my.cnf
[mysqld]
wait_timeout=10
# service mysqld restart
不过这个方法太生硬了, 线上服务重启无论如何都应该尽可能避免.
 
2) 设置全局变量方法2(推荐): 在命令行里通过SET来设置, 然后再修改参数文件
如果要修改全局变量, 必须要显示指定"GLOBAL"或者"@@global.", 同时必须要有SUPER权限. 
mysql> set global wait_timeout=10;
or
mysql> set @@global.wait_timeout=10;
 
然后查看设置是否成功:
mysql> select @@global.wait_timeout=10;
or
mysql> show global variables like 'wait_timeout';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 10    | 
+---------------+-------+
如果查询时使用的是show variables的话, 会发现设置并没有生效, 除非重新登录再查看. 这是因为使用show variables的话就等同于使用show session variables, 查询的是会话变量, 只有使用show global variables查询的才是全局变量. 如果仅仅想修改会话变量的话, 可以使用类似set wait_timeout=10;或者set session wait_timeout=10;这样的语法. 
当前只修改了正在运行的MySQL实例参数, 但下次重启mysqld又会回到默认值, 所以别忘了修改参数文件:
# vi /etc/my.cnf
[mysqld]
wait_timeout=10
 
3) 设置会话变量方法: 在命令行里通过SET来设置
如果要修改会话变量值, 可以指定"SESSION"或者"@@session."或者"@@"或者"LOCAL"或者"@@local.", 或者什么都不使用. 
mysql> set wait_timeout=10;
or
mysql> set session wait_timeout=10;
or
mysql> set local wait_timeout=10;
or
mysql> set @@wait_timeout=10;
or
mysql> set @@session.wait_timeout=10;
or
mysql> set @@local.wait_timeout=10;
or
mysql> set @@local.wait_timeout=10;
 
然后查看设置是否成功:
mysql> select @@wait_timeout;
or
mysql> select @@session.wait_timeout;
or
mysql> select @@local.wait_timeout;
or
mysql> show variables like 'wait_timeout';
or
mysql> show local variables like 'wait_timeout';
or
mysql> show session variables like 'wait_timeout';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 10    | 
+---------------+-------+
 
4) 会话变量和全局变量转换方法: 在命令行里通过SET来设置
将会话变量值设置为对应的全局变量值呢:
mysql> set @@session.wait_timeout=@@global.wait_timeout;
将会话变量值设置为MySQL编译时的默认值(wait_timeout=28800):
mysql> set wait_timeout=DEFAULT;
这里要注意的是, 并不是所有的系统变量都能被设置为DEFAULT, 如果设置这些变量为DEFAULT则会返回错误. 
```