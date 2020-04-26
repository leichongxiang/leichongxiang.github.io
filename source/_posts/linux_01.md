title: linux小知识
date: 2016-03-14 17:30:35
tags:
 - linux
categories: [linux]
photos:
 - http://ww3.sinaimg.cn/large/8e712944jw1f1wjdty31qj209706odfv.jpg
---

查看服务器网络状态并汇总
```
[root@game01 ~]# netstat -an|awk '/^tcp/ {++a[$NF]} END{for (i in a) print i,a[i]}'
TIME_WAIT 38
ESTABLISHED 46
LISTEN 12
```

看到这么一道题，求平均和个数
```
cat a.txt 
a,2
a,3
b,4
b,5
c,6
d,7
#结果
awk -F, '{a[$1]+=$2 }{cnt[$1]++}END{for(i in a) print i,cnt[i],a[i],a[i]/cnt[i]}' a.txt
a 2 5 2.5
b 2 9 4.5
c 1 6 6
d 1 7 7

```

<!--more -->

整理一下top的几个常用参数
- T：根据时间，累计时间排序
- M：根据内存的大小排序
- P：根据CPU使用的多少排序
- t：切换显示进程和CPU的状态信息
- c：切换显示命令名称和完整命令
- m：切换显示内存




