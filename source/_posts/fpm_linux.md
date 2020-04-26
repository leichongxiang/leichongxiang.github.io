title: fpm打rpm包
date: 2017-02-10 10:30:35
tags:
 - fpm
categories: [自动化]

---
## 说明

<div style="border-radius:10px; width: 90%; border:10px solid #AFEEEE;font-size:16px;align=left;  background:#AFEEEE">编译源码，根据自己的需求做成定制RPM包–>搭建内网yum仓库–yum安装。结合前两者的优点，暂未发现什么缺点。可能的缺点就是RPM包的通用性差，只能适用于本公司的环境。</div>

<!--more -->

## FPM安装
fpm是ruby写的，因此系统环境需要ruby，且ruby版本号大于1.8.5。

## 安装ruby模块
```
yum -y install ruby rubygems ruby-devel gcc
添加阿里云的Rubygems仓库，外国的源慢

gem sources -a http://mirrors.aliyun.com/rubygems/
移除原生的Ruby仓库

gem sources --remove http://rubygems.org/
安装fpm

gem install fpm
```
报错

```
root@localhost ~ # gem install fpm                                   
ERROR:  Error installing fpm:
        ruby-xz requires Ruby version >= 1.9.3.
```
        
解决办法1: 更新ruby

```
安装ruby
root@localhost ~ # wget http://ftp.ruby-lang.org/pub/ruby/2.3/ruby-2.3.0.tar.gz
tar -zxvf ruby-2.3.0.tar.gz
cd ruby-2.3.0/ 
./configure --enable-shared --enable-pthread --prefix=/usr/local/ruby
make && make install
```

注意配置环境变量

```
ruby -v

gem install fpm
```
解决办法2: 安装旧版本的fpm


`[root@ops-rpmbuild01 ~]# gem install fpm -v 1.4.0`
简单实验
打包一个文件为例子。实现用户安装rpm将会把index.html放到/opt/lanxin/servers/a.html.

```
mkdir /tmp/rpmbuild
mkdir /tmp/rpmbuild/opt/lanxin/server/xhjx -p
touch /tmp/rpmbuild/opt/lanxin/server/xhjx/a.html

fpm -s dir -t rpm -n xhjx -v 1.0.1 -C /tmp/rpmbuild/

打包出来的rpm包
xhjx-1.0.1-1.x86_64.rpm  

查看rpm包的内容
rpm -qpl xhjx-1.0.1-1.x86_64.rpm
/opt/lanxin/server/xhjx/a.html

安装rpm包
rpm -ivh xhjx-1.0.1-1.x86_64.rpm
就会/opt/lanxin/server/xhjx/a.html
```
## 重点讲解fpm
FPM的作者是jordansissel
FPM的github：https://github.com/jordansissel/fpm
FPM功能简单说就是将一种类型的包转换成另一种类型

### 用法：
`fpm -s <source type> -t <target type> [options]`

1. 支持的源类型包
dir 将目录打包成所需要的类型，可以用于源码编译安装的软件包
rpm 对rpm进行转换
gem 对rubygem包进行转换
python 将python模块打包成相应的类型

2. 支持的目标类型包
rpm 转换为rpm包
deb 转换为deb包
solaris 转换为solaris包
puppet 转换为puppet模块

3. FPM参数
fpm –help
常用的参数

```
-s          指定源类型
-t          指定目标类型，即想要制作为什么包
-n          指定包的名字
-v          指定包的版本号
-C          指定打包的相对路径  Change directory to here before searching forfiles
-d          指定依赖于哪些包
-f          第二次打包时目录下如果有同名安装包存在，则覆盖它
-p          输出的安装包的目录，不想放在当前目录下就需要指定
--post-install      软件包安装完成之后所要运行的脚本；同--after-install
--pre-install       软件包安装完成之前所要运行的脚本；同--before-install
--post-uninstall    软件包卸载完成之后所要运行的脚本；同--after-remove
--pre-uninstall     软件包卸载完成之前所要运行的脚本；同--before-remove
```
## 使用实例–实战定制nginx的RPM包
### 安装nginx

```
yum -y install pcre-devel openssl-devel
useradd nginx -M -s /sbin/nologin
tar xf nginx-1.6.2.tar.gz
cd nginx-1.6.2
./configure --prefix=/application/nginx-1.6.2 --user=nginx --group=nginx --with-http_ssl_module --with-http_stub_status_module
make && make install
ln -s /application/nginx-1.6.2/ /application/nginx
```
### 编写脚本

```
[root@leicx ~]# cd /server/scripts/
[root@leicx scripts]# vim nginx_rpm.sh  # 这是安装完rpm包要执行的脚本
#!/bin/bash
useradd nginx -M -s /sbin/nologin
ln -s /application/nginx-1.6.2/ /application/nginx
```
### 打包

```
[root@leicx ~]# fpm -s dir -t rpm -n nginx -v 1.6.2 -d 'pcre-devel,openssl-devel' --post-install /server/scripts/nginx_rpm.sh -f /application/nginx-1.6.2/  
no value for epoch is set, defaulting to nil {:level=>:warn}
no value for epoch is set, defaulting to nil {:level=>:warn}
Created package {:path=>"nginx-1.6.2-1.x86_64.rpm"}
[root@leicx ~]# ll -h nginx-1.6.2-1.x86_64.rpm 
-rw-r--r-- 1 root root 6.7M Nov  1 10:02 nginx-1.6.2-1.x86_64.rpm
```
### 安装rpm包

安装rpm包的三种方法：

#### 1.rpm命令安装

```
[root@LB-nginx-01 ~]# rpm -ivh nginx-1.6.2-1.x86_64.rpm
error: Failed dependencies:
     pcre-devel is needed by nginx-1.6.2-1.x86_64
     openssl-devel is needed by nginx-1.6.2-1.x86_64
但会报如上依赖错误，需要先yum安装依赖才能安装rpm包。
```
#### 2.yum命令安装rpm包
```
yum -y localinstall nginx-1.6.2-1.x86_64.rpm
这个命令会自动先安装rpm包的依赖，然后再安装rpm包。
```
#### 3.搭建内网yum仓库
YUM仓库搭建

## 注意事项
相对路径问题

```
# 相对路径
[root@leicx nginx]# fpm -s dir -t rpm -n nginx -v 1.6.2 .
no value for epoch is set, defaulting to nil {:level=>:warn}
no value for epoch is set, defaulting to nil {:level=>:warn}
Created package {:path=>"nginx-1.6.2-1.x86_64.rpm"}
[root@leicx nginx]# rpm -qpl nginx-1.6.2-1.x86_64.rpm    
/client_body_temp
/conf/extra/dynamic_pools
/conf/extra/static_pools
  …………
# 绝对路径
[root@leicx ~]# fpm -s dir -t rpm -n nginx -v 1.6.2 /application/nginx-1.6.2/
no value for epoch is set, defaulting to nil {:level=>:warn}
no value for epoch is set, defaulting to nil {:level=>:warn}
Created package {:path=>"nginx-1.6.2-1.x86_64.rpm"}
[root@leicx ~]# rpm -qpl nginx-1.6.2-1.x86_64.rpm 
/application/nginx-1.6.2/client_body_temp
/application/nginx-1.6.2/conf/extra/dynamic_pools
/application/nginx-1.6.2/conf/extra/static_pools
/application/nginx-1.6.2/conf/fastcgi.conf
/application/nginx-1.6.2/conf/fastcgi.conf.default
…………
```
使用rpm -qpl 命令可以查看rpm包的内容。
注：fpm类似tar打包一样，只是fpm打的包能够被yum命令识别而已。
软链接问题

```
[root@leicx ~]# fpm -s dir -t rpm -n nginx -v 1.6.2 /application/nginx
no value for epoch is set, defaulting to nil {:level=>:warn}
File already exists, refusing to continue: nginx-1.6.2-1.x86_64.rpm {:level=>:fatal}
# 报错是因为当前目录存在同名的rpm包，可以使用-f参数强制覆盖。
[root@leicx ~]# fpm -s dir -t rpm -n nginx -v 1.6.2 -f /application/nginx
no value for epoch is set, defaulting to nil {:level=>:warn}
Force flag given. Overwriting package at nginx-1.6.2-1.x86_64.rpm {:level=>:warn}
no value for epoch is set, defaulting to nil {:level=>:warn}
Created package {:path=>"nginx-1.6.2-1.x86_64.rpm"}
打包看似成功，但查看包的内容，只是这一个软链接文件。
[root@leicx ~]# rpm -qpl nginx-1.6.2-1.x86_64.rpm 
/application/nginx
原因：目录结尾的/问题，类似rm删除软链接目录
fpm -s dir -t rpm -n nginx -v 1.6.2 -d 'pcre-devel,openssl-devel' --post-install /server/scripts/nginx_rpm.sh -f /application/nginx-1.6.2/
```
## 定制LNMP的RPM包思路
编译安装好nginx，mysql，php，此处有个问题，就是php的大部分依赖环境是通过yum安装的，但有一个libiconv-1.14.tar.gz包需要编译安装,安装时已经指定了安装目录，只需一同打包即可。

还有一个问题，就是mysql这个目录比较大，用fpm打包耗时长。平时我们有可能需要对nginx或php做优化，这样又得重新打包。因此我们可以将mysql分离出来，分别打包。只需在制作nginx+php的rpm包时添加mysql的依赖即可。

参考命令
```
 [root@web2 ~]# fpm -s dir -t rpm -n web2 -v 1.1 \
--description 'lnmp.cms,bbs.blog' \
-d ‘libxslt-devel,nfs-utils,rpcbind,mysql,libmcrypt-devel,mhash,mhash-devel,mcrypt' \
--post-install /server/scripts/lnmp-init.sh  \
/application /usr/local/libiconv/ /app/logs/ /data0/  /server/
```
