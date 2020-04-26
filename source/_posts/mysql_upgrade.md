title: mysql升级
date: 2016-01-22 16:30:35
tags:
 - mysql升级
categories: [mysql]

---
我们先做一些声明，MySQL采用二进制包来安装，升级都是在同一台DB Server上操作
# 升级注意事项 
在升级 MySQL 时需要注意的事项：
- 仔细阅读一下升级的目标版本的新特性和改变的特性，以及2个版本之间的不同特性
- 升级前一定要备份所有的数据
- 有些不同版本间的升级可能会涉及对授权表的修改
- 同步数据的兼容性

# 方法一
`适用于任何存储引擎。`
1. 下载并安装好新版本的MySQL数据库，并将其端口改为3307（避免和旧版本的3306冲突），启动服务。

2. 在新版本下创建同名数据库。
```
# mysqldump -p3307 -uroot create mysqlsystems_com
```
3. 在旧版本下备份该数据库。
```
# mysqldump -p3306 -uroot mysqlsystems_com > mysqlsystems_com.bk
Note: 你也可以加上–opt选项，这样可以使用优化方式将你的数据库导出，减少未知的问题。
```
4. 将导出的数据库备份导入到新版本的MySQL数据库中。
```
# mysql -p3307 -uroot mysqlsystems_com < mysqlsystems_com.bk
```
5. 再将旧版本数据库中的data目录下的mysql数据库全部覆盖到新版本中。
```
# cp -R /opt/mysql-5.1/data/mysql /opt/mysql-5.4/data
Note: 大家也都知道这个默认数据库的重要性。
```
6. 在新版下执行mysql_upgrade命令，其实这个命令包含一下三个命令：
```
# mysqlcheck –check-upgrade –all-databases –auto-repair
# mysql_fix_privilege_tables
# mysqlcheck –all-databases –check-upgrade –fix-db-names –fix-table-names
```
Note: 在每一次的升级过程中，mysql_upgrade这个命令我们都应该去执行，它通过mysqlcheck命令帮我们去检查表是否兼容新版本的数据库同时作出修复，还有个很重要的作用就是使用mysql_fix_privilege_tables命令去升级权限表。

7. 关闭旧版本，将新版的数据库的使用端口改为3306，重新启动新版本MySQL数据库。到此，一个简单环境下的数据库升级就结束了。

# 方法二
同样适用任何存储引擎。
1. 同样先安装好新版本的MySQL。

2. 在旧版本中，备份数据库。
```
# mkdir /opt/mysqlsystems_bk ;
# mysqldump -p3306 -uroot –tab=/opt/mysqlsystems_bk mysqlsystems_com
```
Note:-tab选项可以在备份目录mysqlsystems_bk下生成后缀为*.sql和*.txt的两类文件；其中，.sql保存了创建表的SQL语句而.txt保存着原始数据。

3. 接下来在新版本的数据库下更新数据。
```
# mysqladmin -p3307 -uroot create mysqlsystems_com
# cat /opt/mysqlsystems_bk/*.sql | mysql -p3307 -uroot mysqlsystems_com       ( Create Tables )
# mysqlimport mysqlsystems_com /opt/mysqlsystems_bk/*.txt            ( Load Data )
```
4. 之后的所有步骤与第一种方法的后三步5、6、7相同。

# 方法三
第三种，适用于MyISAM存储引擎，全部是文件间的拷贝。
1. 安装。
2. 从旧版本mysqlsystems_com数据库下将所有`.frm、.MYD 和.MYI文件`拷贝到新版本的相同目录下。
3.之后的步骤依然同于第一种的后三步。

以上就是三种升级MySQL的方法，看似没有出现什么问题，其实，在实际的生产环境中，为会有诸多问题发生，这就需要我们在升级之前充分了解新版本中增加了哪些新功能，进一步分析升级以后这些新特性是否将会对我们原来应用产生影响

