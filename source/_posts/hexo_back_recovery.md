title: 多台电脑之间维护hexo博客
date: 2016-05-14 10:30:35
tags:
 - hexo
categories: [hexo]

---

## 说明

<div style="border-radius:10px; width: 90%; border:10px solid #AFEEEE;font-size:16px;align=left;  background:#AFEEEE">由于在最初的部署hexo的时候已经将back插件hexo-git-backup备份工具，所以这篇文章只是简单介绍一下恢复的方法。</div>

## 场景
单位和家里两PC，同时都想更新blog。而由于hexo没有后台，而且全部文件都在本地生成，所以如果公司电脑上发表了A文章后回家又写了篇B文章，在家里上传后你会发现只有B文章而A文章没了（因为家里的PC上没有A文章的md文件），所以多台电脑同时用来写文章的时候，需要解决备份问题。

<!--more -->

## 配置过程
### 上传blog到git

这个操作建议在blog进度最新的PC上进行的，否则后面解决冲突会比较麻烦
在osc上添加公钥，建立新respo等过程略过不讲。

#### 1.删除文件夹内原有的.git缓存文件夹并编辑.gitignore文件

有些插件或者主题是git上下过来安装的话，每个文件夹下都会有对应的.git 文件夹，记得先删掉，否则会和blog仓库冲突
（.git默认是隐藏文件夹，需要先开启显示隐藏文件夹。##.git文件夹被删除后整个文件对应的git仓库状态也会被清空##)
.gitignore文件作用是声明不被git记录的文件，blog根目录下的.gitignore是hexo初始化带来的，可以先删除或者直接编辑，对hexo不会有影响。建议.gitignore内添加以下内容：
```
/.deploy_git
/public  
/_config.yml

```
.deploy_git是hexo默认的.git配置文件夹，不需要同步
public内文件是根据source文件夹内容自动生成，不需要备份，不然每次改动内容太多
即使是私有仓库，除去在线服务商员工可以看到的风险外，还有云服务商被攻击造成泄漏等可能，所以不建议将配置文件传上去

#### 2.初始化仓库

blog根目录下执行以下代码：
```bash
git init
git remote add origin <server>
<server>是指在线仓库的地址。origin是本地分支,remote add操作会将本地仓库映射到云端
```
#### 3.添加本地文件到仓库并同步到git上
```bash
git add .  #添加blog目录下所有文件，注意有个`.`（`.gitignore`声明过的文件不包含在内)
git commit -m "first commit" #添加更新说明
git push -u origin master #推送更新到云端服务器
```
在执行这步之前一定要注意检查下.gitignore文件的内容，看看是否正确的把一些文件夹忽略掉了。如果加错了的话可以用
```
git rm -r --cached .
```
撤销添加操作。

到这里的时候，云端备份已经完成

### 将git的内容同步到本地

假设之前将A电脑里的内容备份到git了，现在B电脑准备同步内容。
```
git init
git remote add origin <server> #将本地文件和云端仓库映射起来。这步不可以跳过
git fetch --all
git reset --hard origin/master
```
fetch是将云端所有内容拉取下来。reset则是不做任何合并处理，强制将本地内容指向刚刚同步下来的云端内容（正常pull的话需要考虑不少冲突的问题，比较麻烦。）

### 更新文章后的同步操作

假设在B电脑上写完了文章，也hexo d -g发布完了，这时候需要将新文章的md文件更新上去。（其实就是提交更新给git，会的可以无视了）
同一个bash界面下：
```
git add .
```
这时候可以用git status查看状态，一般会显示刚刚更改过的文件状态。如：
```
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   db.json
        new file:   source/_posts/test.md
上面的输出状态即说明’db.json’文件做了更改，source/_posts目录下新增了’test.md’文件。
```
然后对更改添加说明并推送到远程仓库.
```
git commit -m '更新信息'
git push
```
当显示类似如下提示的时候，即表示备份成功
```
To git@git.oschina.net:xxxx/blog-backup.git
 + 2c77e1e...5616bc6 master -> master (forced update)
 ```
再到A电脑上的时候，只需要
```
git pull
```
即可同步更新

### 总结
如果之前在公司和家里的电脑上都已经有之前的blog文件 只需要执行`git pull`就能同步。
