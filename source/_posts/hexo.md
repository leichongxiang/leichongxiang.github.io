title: hexo
date: 2015-10-21 14:07:35
tags:
 - hexo
categories: [hexo]

---
# 搭建hexo参考文档

```
hexo官网:  https://hexo.io/zh-cn/docs/
next官网： http://theme-next.iissnan.com/
hexo+next:
  https://blog.lmintlcx.com/post/blog-with-hexo.html
  http://chitanda.me/2015/06/11/tips-for-setup-hexo/
  http://hifor.net/2015/07/01/零基础免费搭建个人博客-hexo-github/

git备份: https://github.com/coneycode/hexo-git-backup
添加二维码： http://hujiandong.com/2015/10/15/how_add_weixinpublic/?utm_source=tuicool&utm_medium=referral
```
<!-- more -->

# markdown编写博文的语法参考

```
markdown官网：http://www.appinn.com/markdown/
markdown博文:
http://ibruce.info/2013/11/26/markdown/
http://www.jianshu.com/p/1e402922ee32/
```
# 常用的git命令

```
ssh-keygen -t  rsa -C "your_mail@gmail.com"
ssh-keygen -t  rsa -C "your_mail@gmail.com" -f ~/.ssh/gitcafe

git config --global user.name "name"
git config --global user.email "your_mail@gmail.com"

#提交检出均不转换
git config --global core.autocrlf false

git同步远程分支
git clone -b blog_source git@git.oschina.net:leicx/blog.git


```

# 添加音乐
这里边是一个音乐测试盒
## 豆瓣音乐
添加以下代码在md文件中
```html
<center> <iframe name="iframe_canvas" src="http://douban.fm/partner/baidu/doubanradio" scrolling="no" frameborder="0" width="400" height="200"></iframe> </center>

```

## 网易音乐
添加以下代码在md文件中
```html
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=29431066&auto=0&height=66"></iframe>
auto=0  停止
auto=1  播放
```
# 添加图片
![雪景](/img/04.jpg)

**沁园春·雪**

*作者：毛泽东*

>北国风光，千里冰封，万里雪飘。
望长城内外，惟余莽莽；大河上下，顿失滔滔。
山舞银蛇，原驰蜡象，欲与天公试比高。
须晴日，看红装素裹，分外妖娆。
江山如此多娇，引无数英雄竞折腰。
惜秦皇汉武，略输文采；唐宗宋祖，稍逊风骚。
一代天骄，成吉思汗，只识弯弓射大雕。
俱往矣，数风流人物，还看今朝。


# 代码
```python
void BubbletSort(int*a,int len) {
    int m;
    for (bool bSwap=true; bSwap; len++) {
        bSwap = false;
        for (int j=1;ja[j]) {
                m=a[j];
                a[j]=a[j-1];
                a[j-1]=m;
                bSwap=true;
            }
        }
    }
}
```
