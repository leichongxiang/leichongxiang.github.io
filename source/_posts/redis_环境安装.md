title: redis安装启动
date: 2016-01-12 18:00:35
tags:
 - redis
categories: [redis]

---
Redis是一个开源，先进的key-value存储，并用于构建高性能，可扩展的Web应用程序的完美解决方案。

Redis从它的许多竞争继承来的三个主要特点：
	- Redis数据库完全在内存中，使用磁盘仅用于持久性。
	- 相比许多键值数据存储，Redis拥有一套较为丰富的数据类型。
	- Redis可以将数据复制到任意数量的从服务器。
<!--more -->

Redis 优势
	- 异常快速：Redis的速度非常快，每秒能执行约11万集合，每秒约81000+条记录。

	- 支持丰富的数据类型：Redis支持像列表，集合，有序集合，散列数据类型。这使得它非常容易解决各种各样的问题，
因为我们知道哪些问题是可以处理通过它的数据类型更好。

	- 操作都是原子性：所有Redis操作是原子的，这保证了如果两个客户端同时访问的Redis服务器将获得更新后的值。

	- 多功能实用工具：Redis是一个多实用的工具，可以在多个用例如缓存，消息，队列使用(Redis原生支持发布/订阅)，
	任何短暂的数据，应用程序，如Web应用程序会话，网页命中计数等。

# 软件下载
http://download.redis.io/releases/redis-2.8.24.tar.gz

# 源码安装
```bash
tar -zxvf redis-2.8.24.tar.gz
cd redis-2.8.24
make && sudo make install

mkdir -p /usr/local/redis/bin
mkdir -p /usr/local/redis/etc

mv redis.conf /usr/local/redis/etc/
cd src/
mv mkreleasehdr.sh redis-benchmark redis-check-aof redis-check-dump redis-cli redis-server /usr/local/redis/bin

#修改配置文件 
vi /usr/local/redis/etc/redis.conf

将daemonize no 中no改为yes[yes指后台运行]

#启动/随机启动:
cd /usr/local/redis/bin
/usr/local/redis/bin/redis-server /usr/local/redis/etc/redis.conf

#设置随机启动
vi /etc/rc.local
/usr/local/redis/bin/redis-server /usr/local/redis/etc/redis.conf

#查看是否启动成功 
ps -ef | grep redis

#查看端口是否占用。
netstat -tunpl | grep 6379

#进入redis
/usr/local/redis/bin/redis-cli

#关闭redis  
pkill redis-server
或者
/usr/local/redis/bin/redis-cli shutdown
```
说明
redis-benchmark //用于进行redis性能测试的工具
redis-check-dump //用于修复出问题的dump.rdb文件
redis-cli //redis的客户端
redis-server //redis的服务端
redis-check-aof //用于修复出问题的AOF文件
redis-sentinel //用于集群管理
