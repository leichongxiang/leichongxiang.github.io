title: hexo迁移从gitcafe到coding-net
date: 2016-05-04 17:30:35
tags:
 - hexo
categories: [hexo]

---

## 说明

  
<div style="border-radius:10px; width: 90%; border:10px solid #AFEEEE;font-size:16px;align=left;  background:#AFEEEE">2016.05.10更新:部署到coding时，如果使用pages的话，项目名称必须与coding的用户名一致，但是演示并没有这个要求。而且目前为止：演示不能绑定自己的域名，而pages可以，所以说，为了以后方便，项目名称还是与用户名一致的好，不然部署pages会出现没有样式的bug.之前已经把hexo部署到github，但是有时候挺慢的，于是就像跟大家一样搞到国内的gitcafe上，岂料，gitcafe被coding收购，要求迁移</div>

<!--more -->
   
## 迁移注意点
### 1.在coding中添加ssh-key
修改ssh的生成key的配置要求
```bash
67uyuk@leicx MINGW32 /d/blog (master)
$ vim ~/.ssh/config

Host git.coding.net
User *****@163.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/coding_rsa
```
生成公钥
```bash
ssh-keygen -t rsa -b 4096 -C "******@163.com" -f ~/.ssh/coding_rsa
```
拷贝对应的公钥到coding中
测试
```bash
$ ssh -T git@git.coding.net
Hello vikelee You've connected to Coding.net by SSH successfully!

```
### 2.修改hexo的配置文件

```bash
67uyuk@leicx MINGW32 /d/blog (master)
$ vim _config.yml
deploy:
  type: git
  message: "deploy finished"
  repo:
    github: git@github.com:leichongxiang/leichongxiang.github.io.git,master
    coding: git@git.coding.net:vikelee/vikelee.git,coding-pages

```
`格式说明：coding: git@git.coding.net:vikelee/vikelee.git,coding-pages`
ssh的coding地址，分支名称，这块必须是`coding-pages`,因为coding的page页的分支是coding-pages

### 3.设置项目名称
<font color="red">项目名称还是与用户名一致的好，不然部署pages会出现没有样式的bug</font>

### 4.再blog中添加Staticfile文件
在博客的source/目录下需要创建一个空白文件,至于原因，是因为 coding.net需要这个文件来作为以静态文件部署的标志。就是说看到这个Staticfile就知道按照静态文件来发布。
```bash
cd source/
touch Staticfile  #名字必须是Staticfile

hexo clean
hexo g
hexo d
```
![coding](http://7xnmpy.com1.z0.glb.clouddn.com/blog/imge/04.jpg)

然后一切都正常了。
