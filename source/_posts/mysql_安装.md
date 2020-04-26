title: mysql安装
date: 2016-01-18 16:30:35
tags:
 - mysql
categories: [mysql]

---
在日常运维的工作中，数据库的运维是必不可少的，从今天起未来一周都会专心总结mysql的应用。今天主要介绍mysql的安装，从源码包，和rpm包介绍。

# 源码包安装
可以去[mysql官网](http://www.mysql.com/downloads/)下载源码包。
<!--more -->
```bash
[root@MySQL-Master src]# tar zxvf mysql-5.1.63.tar.gz
[root@MySQL-Master src]# cd mysql-5.1.63
[root@MySQL-Master mysql-5.1.63]# ./configure --prefix=/usr/local/mysql \
--localstatedir=/data/mysql --enable-assembler \
--with-client-ldflags=-all-static --with-mysqld-ldflags=-all-static \
--with-pthread --enable-static --with-big-tables --without-ndb-debug \
--with-charset=utf8 --with-extra-charsets=all \
--without-debug --enable-thread-safe-client --enable-local-infile --with-plugins=max
[root@MySQL-Master mysql-5.1.56]# make && make install
```
## 安装参数介绍
--prefix=/usr/local/mysql //主程序安装目录
--localstatedir=/data/mysql //数据文件存放目录
--with-client-ldflags=-all-static --with-mysqld-ldflags=-all-static//静态编译安装 mysql 客户端和服务端
--with-pthread //采用线程
--with-big-tables //对大表的支持
--with-charset=utf8 //默认字符集为 utf8
--with-extra-charsets=all //安装所有字符集
--without-debug //去掉 debug 模式
--enable-thread-safe-client //以线程方式编译客户端
--with-plugins=max //添加对 innodb 及 partition 的支持
--enable-local-infile //对 load data 的支持
## 创建用户和组
```bash
[root@MySQL-Master mysql-5.1.56]# groupadd mysql
[root@MySQL-Master mysql-5.1.56]# useradd -s /sbin/nologin -M -g mysql mysql
```
## 安装数据库
```bash
[root@MySQL-Master mysql-5.1.56]# cd /usr/local/mysql/
[root@MySQL-Master mysql]# mkdir -p /data/mysql
[root@MySQL-Master mysql]# bin/mysql_install_db --basedir=/usr/local/mysql/ --datadir=/data/mysql/
--user=mysql
```
## 相应权限的修改
```bash
[root@MySQL-Master mysql]# chown -R root:mysql /usr/local/mysql/
[root@MySQL-Master mysql]# chown -R mysql:mysql /data/mysql/
```
## 配置文件
```bash
[root@MySQL-Master mysql]# cp /usr/local/mysql/share/mysql/my-medium.cnf /etc/my.cnf
[root@MySQL-Master mysql]# cp /usr/local/mysql/share/mysql/mysql.server /etc/init.d/mysqld
[root@MySQL-Master mysql]# chmod 755 /etc/init.d/mysqld
[root@MySQL-Master mysql]# chkconfig --add mysqld
[root@MySQL-Master mysql]# vim /root/.bash_profile
PATH=$PATH:$HOME/bin:/usr/local/mysql/bin
[root@MySQL-Master mysql]# source /root/.bash_profile
```
## 启动数据库并初始化密码。
```bash
[root@MySQL-Master mysql]# service mysqld start
Starting MySQL [ OK ]
[root@MySQL-Master mysql]# mysqladmin -u root password unixhot //设置成自己的密码
```

# rpm安装mysql
## 安装mysql
```bash
yum install mysql mysql-server
chkconfig --levels 235 mysqld on //开机启动
/etc/init.d/mysqld start
```
注意：从centos7.0开始，yum软件库中不再有mysql-server，而是由mariaDB取代，mariaDB与mysql完全兼容。
在centos7.0中安装mysql，可以在MySQL Yum Repository找到yum安装源：
http://dev.mysql.com/downloads/repo/yum/
```bash
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum install mysql-community-server
```

