title: dockerfile的注意事项
date: 2017-02-10 11:30:35
tags:
 - docker
categories: [docker]

---
## 说明


<div style="border-radius:10px; width: 90%; border:10px solid #AFEEEE;font-size:16px;align=left;  background:#AFEEEE">dockerfile 编写需要注意的地方。</div>

<!--more -->

## 1. 避免使用 apt-get upgrade
upgrade 命令会打乱已经缓存的镜像，使得编译时间加长。如果要安装某个包`apt-get install foo -y&& rm -rf /var/lib/apt/lists/*`,安装完成之后需要删除本地的缓存。


## 2. .dockerignore 文件
在 docker build 的时候，忽略部分无用的文件和目录可以提高构建的速度,将他们写进.dockerignore。

## 3.不要在构建中升级版本
不在容器中更新，更新交给基础镜像来处理。

## 4.每个容器只运行一个进程
一个容器只运行一个进程。容器起到了隔离应用隔离数据的作用，不同的应用运行在不同的容器让集群的扩展变的更加简单。

## 5.最小化层数
需要掌握号Dockerfile的可读性和文件系统层数之间的平衡。最多不能超过42层。只有RUN、COPY和ADD创建层，其他指令只是创建临时的中间镜像，不会直接增加镜像大小

## 6.使用小型基础镜像
基础镜像足够小并且没有包含任何不需要的包。最好使用官方源中的镜像做为基础镜像，推荐使用Alpine镜像，因为它只有5mb



## 7.多行参数顺序
采取 \ 进行多行安装。同时需要注意按照字母表顺序排序避免出现重复。

## 8.尽量合并命令
Dockerfile 中的每一个命令都会创建一个新的 layer，而一个容器能够拥有的最多 layer 数是有限制的。所以尽量将逻辑上连贯的命令合并可以减少 layer的层数，合并命令的方法可以包括将多个可以合并的命令（EXPOSE， ENV，VOLUME，COPY）合并。

`EXPOSE 80`
`EXPOSE 8000`
合并为
`EXPOSE 80 8000`

`RUN foo`
`RUN bar`
合并为
`RUN foo && bar`

## 9.合理安排命令的顺序

命令的顺序会影响编译所需要的时间。每一个命令都会产生一个 layer。如果一个 layer 已经在缓存中，那么生成这个 layer 所需要的时间就很短。从第一个不在缓存的 layer 起，所有以后的命令都会被重新编译。因为这个原因，推荐将不常变动的命令放在前面，这样可以使得更多的 layer 被成功缓存，从而减少编译时间。

## 10.避免在容器中存储数据

容器需要是无状态的，这样方便启动新的 Container 来替代 down 掉的 Container。

## 11. 避免安装不必要的软件
安装不必要的软件既浪费空间又增加编译时间。

## 12. 使用多阶段构建
（multi-stage build），仅限Docker17.05之后的版本，即:使用多个FROM指令

```
对于多阶段构建的一个例子：

FROM golang:1.7.3 as builder
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html
COPY app.go    .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /go/src/github.com/alexellis/href-counter/app .
CMD ["./app"]
```

## 13.RUN中使用管道符要注意

由于Docker只关注最好一个命令执行是否正确，所以当管道前面错误时，后面依然可能成功，所以应该设置set -o pipefail &&来使执行过程中任何产生的错误都失败。
如下：
```
RUN ["/bin/bash", "-c", "set -o pipefail && wget -O - https://some.site | wc -l > /number"]
```
## 14. 关于sudo
尽量避免安装和使用 sudo，如果一定要使用类似 sudo 功能，可以使用 gosu 替代它，为了减少层数和复杂度，避免频繁使用 USER 进行用户切换。
