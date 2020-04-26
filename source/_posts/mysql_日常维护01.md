title: mysql日常维护01
date: 2016-01-19 11:30:35
tags:
 - mysql
categories: [mysql]

---
这篇博客主要总结mysql操作中常见的需求，归纳起来以便于分享给大家。

# 修改密码
格式：`mysqladmin -u用户名 -p旧密码 password 新密码`

例1：给root加个密码ab12。
`mysqladmin -uroot -password ab12`
注：因为开始时root没有密码，所以-p旧密码一项就可以省略了。

例2：再将root的密码改为djg345。
`mysqladmin -uroot -pab12 password djg345`

<!--more -->

# 增加新用户
格式：`grant all on 数据库.* to 用户名@登录主机 identified by \"密码\"`

例1、增加一个用户test1密码为abc，让他可以在任何主机上登录，并对所有数据库有查询、插入、修改、删除的权限。
`grant select,insert,update,delete on *.* to  identified by \"abc\";`

如果你不想test2有密码，可以再打一个命令将密码消掉。
```bash
grant select,insert,update,delete on mydb.* to identified by \"\";
```
也可以使用该表法，修改mysql.user表
```bash
mysql> INSERT INTO user (host, user, password, select_priv, insert_priv, update_priv) VALUES ('localhost', 'guest', PASSWORD('guest123'), 'Y', 'Y', 'Y');
Query OK, 1 row affected (0.20 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 1 row affected (0.01 sec)
```

# 配置mysql允许远程链接
默认情况下，mysql帐号不允许从远程登陆，只能在localhost登录。本文提供了二种方法设置mysql可以通过远程主机进行连接。
## 改表法
```bash
mysql>update user set host = '%' where user = 'root';
mysql>select host, user from user;
```

## 授权法
你想myuser使用mypassword（密码）从任何主机连接到mysql服务器的话。
```bash
mysql>GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%'IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
mysql>FLUSH PRIVILEGES
```
WITH GRANT OPTION的意思：
权限传递
使用这个子句时将允许用户将其权限分配给他人

#  with grant option与with admin option区别
**相同点:**
- 两个都可以既可以赋予user 权限时使用，也可以在赋予role 时用
GRANT CREATE SESSION TO emi WITH ADMIN OPTION;
GRANT CREATE SESSION TO role WITH ADMIN OPTION;
GRANT role1 to role2 WITH ADMIN OPTION;
GRANT select ON customers1 TO bob WITH GRANT OPTION;
GRANT select ON customers1 TO hr_manager(role) WITH GRANT OPTION;

- 两个受赋予者，都可以把权限或者role 再赋予other users
- 两个option 都可以对DBA 和APP ADMIN 管理带来方便性，但同时，都带来不安全的因素

**不同点：**
- with admin option 只能在赋予 system privilege 的时使用
- with grant option 只能在赋予 object privilege 的时使用
- 撤消带有admin option 的system privileges 时，连带的权限将保留
例如：
1. DBA 给了CREATE TABLE 系统权限给JEFF WITH ADMIN OPTION
2. JEFF CREATES TABLE
3. JEFF grants the CREATE TABLE 系统权限给EMI
4. EMI CREATES A table
5. DBA 撤消CREATE TABLE 系统权限从JEFF
结果：
JEFF‘S TABLE 依然存在，但不能创建新的TABLE 了
EMI’S TABLE 依然存在，他还保留着CREATE TABLE 系统权限。

- 撤消带有grant option 的object privileges 时，连带的权限也将撤消

例如：
1. JEFF 给了SELECT object privileges 在EMP 上 WITH GRANT OPTION
2. JEFF 给了SELECT 权限在EMP 上 TO EMI
3. 后来，撤消JEFF的SELECT 权限

结果：
EMI 的权限也被撤消了

# 删除授权
revoke 跟 grant 的语法差不多，只需要把关键字 “to” 换成 “from” 即可：
```bash
mysql> revoke all privileges on *.* from  dba@localhost; 
mysql> delete from user where user="root" and host="%";
mysql> flush privileges;
```

# 重命名表
`mysql > alter table t1 rename t2;`

# mysqldump
```bash
备份数据库
shell> mysqldump -hhostname -uusername -ppassword dbname >dbname_backup.sql
同时备份多个MySQL数据库
shell> mysqldump -hhostname -uusername -ppassword --databases databasename1 databasename2 databasename3 > multibackupfile.sql ##--databases等价于-B

备份表
shell> mysqldump -hhostname -uusername -ppassword dbname tbname >tbname_backup.sql
备份MySQL数据库多张表
shell> mysqldump -hhostname -uusername -ppassword databasename specific_table1 specific_table2 > backupfile.sql

恢复数据库
shell> mysqladmin -h myhost -u root -p create dbname
shell> mysqldump -h host -u root -p dbname < dbname_backup.sql
```



