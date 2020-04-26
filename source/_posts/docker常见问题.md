title: docker常见问题
date: 2018-03-04 11:30:35
tags:
 - docker
categories: [docker]

---
## 说明


<div style="border-radius:10px; width: 90%; border:10px solid #AFEEEE;font-size:16px;align=left;  background:#AFEEEE">Docker 不是虚拟机，容器只是受限进程，而一个容器只应该跑一个主进程。整理了docker中常见的问题。</div>

<!--more -->


## 0. 概述
**Docker 不是虚拟机，容器只是受限进程，而一个容器只应该跑一个主进程**

## 1. docker容器中root用户无法查看其它用户运行进程的PID
docker为了容器的安全，容器中的root权限有限制，与宿主机上root权限是不一样的，启动容器加上--cap-add=SYS_PTRACE，即可看到PID
```bash
--cap-add: Add Linux capabilities
--cap-drop: Drop Linux capabilities
```

## 2. docker容器使用su切换用户时出错
报错信息：

    su: /bin/bash: Resource temporarily unavailable

打Dockerfile时执行如下命令即可:

    sed -i 's/^*/#*/g' /etc/security/limits.d/90-nproc.conf
    echo "*        soft    core    1024000" >> /etc/security/limits.conf

如果* soft nproc 1024这一行不注释，/etc/security/limits.conf中的配置就不会生效
centos7下需注释的文件为/etc/security/limits.d/20-nproc.conf

## 3.容器如何保证内部时间和主机时间一致
方式一：

    RUN echo "Asia/Shanghai" > /etc/timezone && \
        dpkg-reconfigure -f noninteractie tzdata

方式二：

    volumes:
      - "/etc/timezone:/etc/timezone:ro"
      - "/etc/localtime:/etc/localtime:ro"

以alpine镜像为例：

    #定义环境变量
    ENV  TIME_ZONE Asia/Shanghai
    RUN \
    ...
    #安装tzdata安装包
    && apk add --no-cache tzdata \  
    #设置时区
    && echo "${TIME_ZONE}" > /etc/timezone \
    && ln -sf /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime \

最推荐的做法：
一般应用、服务的配置文件里一般都有时区选项，应该根据自己需求把中国时区配上。
比如 mysqld 中的参数 --timezone=Asia/Shanghai；Java 的 -Duser.timezone=Asia/Shanghai JVM 参数，都可以指定上层应用时区，而不依赖于系统默认时区，这也是推荐的做法。

## 4.怎么绑定一个容器名字和镜像的名字
需要在compose.yml文件中设置：

    #镜像的名字
    image: "myahi.v1"
    #容器名字
    container_name: mydns

或者用：

    docker-compose -p 容器名字 up

## 5.批量清理临时镜像文件

    docker rmi $(docker images -q -f dangling=true)
    docker image prune


## 6.批量清理后台停止的容器

     docker rm $(docker ps -aq)

## 7.docker和虚拟机的区别？
详细见：[链接地址](https://blog.lab99.org/post/docker-2016-07-14-faq.html#docker-yin-qing-xiang-guan-wen-ti-67)
简单点说：
不要把 Docker 和虚拟机弄混，Docker 容器只是一个进程而已，只不过利用镜像提供的 rootfs 提供了调用所需的 userland 库支持，使得进程可以在受控环境下运行而已，它并没有虚拟出一个机器出来。

## 8. 卸载docker
sudo apt-get remove docker* -y
sudo pip uninstall docker-compose -y
sudo apt-get autoremove -y
sudo apt-get autoclean -y


## 9. 怎么映射宿主端口？Dockerfile 中的EXPOSE和 docker run -p 有啥区别？
Docker中有两个概念，一个叫做 EXPOSE ，一个叫做 PUBLISH 。

EXPOSE 是镜像/容器声明要暴露该端口，可以供其他容器使用。这种声明，在没有设定 --icc=false的时候，实际上只是一种标注，并不强制。也就是说，没有声明 EXPOSE 的端口，其它容器也可以访问。但是当强制 --icc=false 的时候，那么只有 EXPOSE 的端口，其它容器才可以访问。
PUBLISH 则是通过映射宿主端口，将容器的端口公开于外界，也就是说宿主之外的机器，可以通过访问宿主IP及对应的该映射端口，访问到容器对应端口，从而使用容器服务。
EXPOSE 的端口可以不 PUBLISH，这样只有容器间可以访问，宿主之外无法访问。而 PUBLISH 的端口，可以不事先 EXPOSE，换句话说 PUBLISH 等于同时隐式定义了该端口要 EXPOSE。

docker run 命令中的 -p, -P 参数，以及 docker-compose.yml 中的  ports 部分，实际上均是指 PUBLISH。

小写 -p 是端口映射，格式为 [宿主IP:]<宿主端口>:<容器端口>，其中宿主端口和容器端口，既可以是一个数字，也可以是一个范围，比如：1000-2000:1000-2000。对于多宿主的机器，可以指定宿主IP，不指定宿主IP时，守护所有接口。

大写 -P 则是自动映射，将所有定义 EXPOSE 的端口，随机映射到宿主的某个端口。

## 10. 要映射好几百个端口
-p 是可以用范围的：
-p 8001-8010:8001-8010

## 11. -p 后还是无法通过映射端口访问容器里面的服务
容器排查：
首先，当然是检查这个 docker 的容器是否启动正常： docker ps、docker top <容器ID>、docker logs <容器ID>、docker exec -it <容器ID> bash等，这是比较常用的排障的命令；如果是 docker-compose 也有其对应的这一组命令，所以排障很容易。

如果确保服务一切正常，甚至在容器里，可以访问到这些服务，docker ps 也显示出了端口映射成功，那么就需要检查防火墙了。

## 12. 如何让一个容器连接两个网络？
如果是使用 docker run，那很不幸，一次只可以连接一个网络，因为 docker run 的 --network 参数只可以出现一次（如果出现多次，最后的会覆盖之前的）。不过容器运行后，可以用命令 docker network connect 连接多个网络。

假设我们创建了两个网络：

    $ docker network create mynet1
    $ docker network create mynet2

然后，我们运行容器，并连接这两个网络。

    $ docker run -d --name web --network mynet1 nginx
    $ docker network connect mynet2 web

但是如果使用 docker-compose 那就没这个问题了。因为实际上，Docker Remote API 是支持一次性指定多个网络的，但是估计是命令行上不方便，所以 docker run 限定为只可以一次连一个。docker-compose 直接就可以将服务的容器连入多个网络，没有问题。
```
version: '2'
services:
    web:
        image: nginx
        networks:
            - mynet1
            - mynet2
networks:
    mynet1:
    mynet2:
```

## 13. --link 过时不再用了？那容器互联、服务发现怎么办？
不要再使用 --link 以及 docker-compose.yml 中的 links 了。应该使用 docker network，建立网络，而 docker run --network 来连接特定网络。或者使用 version: '2' 的 docker-compose.yml 直接定义自定义网络并使用。

## 14. 使用 HBase/Hadoop 的时候，反向解析总是不对，怎么办？
正向解析会从 /etc/hosts 中取得，而反向解析则更可能走 DNS，于是出现了不一致。

反向解析则不会返回所有结果，而只返回<容器名>.<网络名>。

解决办法很简单，我们现在知道反向域名解析的格式为 <容器名>.<网络名>。那么我们只需要将网络名设为域名就可以了。

```
$ docker network create example.com
$ docker run -it --rm \
    --name wombat \
    --hostname wombat.example.com \
    --network example.com \
    m3adow/nettools
#结果，--name是容器名    
host 172.21.0.2
2.0.21.172.in-addr.arpa domain name pointer wombat.example.com.
```

**需要注意的是，服务名、主机名、容器名这类可用于服务发现的名称，应该尽量使用 非 FQDN，也就是不包含 . 的单一名字，否则在某些情况下会出错。**

## 15. 数据容器、数据卷、命名卷、匿名卷、挂载目录这些都有什么区别？
首先，挂载分为挂载本地宿主目录 和 挂载数据卷(Volume)。而数据卷又分为匿名数据卷和命名数据卷。

绑定宿主目录的概念很容易理解，就是将宿主目录绑定到容器中的某个目录位置。这样容器可以直接访问宿主目录的文件。其形式是

`docker run -d -v /var/www:/app nginx`
这里注意到 -v 的参数中，前半部分是绝对路径。在 docker run 中必须是绝对路径，而在 docker-compose 中，可以是相对路径，因为 docker-compose 会帮你补全路径。

另一种形式是使用 Docker Volume，也就是数据卷。这是很多看古董书的人不了解的概念，不要跟数据容器（Data Container）弄混。数据卷是 Docker 引擎维护的存储方式，使用 docker volume create 命令创建，可以利用卷驱动支持多种存储方案。其默认的驱动为 local，也就是本地卷驱动。本地驱动支持命名卷和匿名卷。

顾名思义，命名卷就是有名字的卷，使用 docker volume create --name xxx 形式创建并命名的卷；而匿名卷就是没名字的卷，一般是 docker run -v /data 这种不指定卷名的时候所产生，或者 Dockerfile 里面的定义直接使用的。

有名字的卷，在用过一次后，以后挂载容器的时候还可以使用，因为有名字可以指定。所以一般需要保存的数据使用命名卷保存。

而匿名卷则是随着容器建立而建立，随着容器消亡而淹没于卷列表中（对于 docker run 匿名卷不会被自动删除）。对于二代 Swarm 服务而言，匿名卷会随着服务删除而自动删除。 因此匿名卷只存放无关紧要的临时数据，随着容器消亡，这些数据将失去存在的意义。

此外，还有一个叫数据容器 (Data Container) 的概念，也就是使用 --volumes-from 的东西。这早就不用了，如果看了书还在说这种方式，那说明书已经过时了。按照今天的理解，这类数据容器，无非就是挂了个匿名卷的容器罢了。

在 Dockerfile 中定义的挂载，是指 匿名数据卷。Dockerfile 中指定 VOLUME 的目的，只是为了将某个路径确定为卷。

我们知道，按照最佳实践的要求，不应该在容器存储层内进行数据写入操作，所有写入应该使用卷。如果定制镜像的时候，就可以确定某些目录会发生频繁大量的读写操作，那么为了避免在运行时由于用户疏忽而忘记指定卷，导致容器发生存储层写入的问题，就可以在 Dockerfile 中使用 VOLUME 来指定某些目录为匿名卷。这样即使用户忘记了指定卷，也不会产生不良的后果。

这个设置可以在运行时覆盖。通过 docker run 的 -v 参数或者 docker-compose.yml 的 volumes 指定。使用命名卷的好处是可以复用，其它容器可以通过这个命名数据卷的名字来指定挂载，共享其内容（不过要注意并发访问的竞争问题）。

比如，Dockerfile 中说 VOLUME /data，那么如果直接 docker run，其 /data 就会被挂载为匿名卷，向 /data 写入的操作不会写入到容器存储层，而是写入到了匿名卷中。但是如果运行时 docker run -v mydata:/data，这就覆盖了 /data 的挂载设置，要求将 /data 挂载到名为 mydata 的命名卷中。所以说 Dockerfile 中的 VOLUME 实际上是一层保险，确保镜像运行可以更好的遵循最佳实践，不向容器存储层内进行写入操作。

数据卷默认可能会保存于 /var/lib/docker/volumes，不过一般不需要、也不应该访问这个位置。

## 16. 为什么绑定了宿主的文件到容器，宿主修改了文件，容器内看到的还是旧的内容啊？
在绑定宿主内容的形式中，有一种特殊的形式，就是绑定宿主文件，既：

`docker run -d -v $PWD/myapp.ini:/app/app.ini myapp`
在 myapp.ini 文件不发生改变的情况下，这样的绑定是和绑定宿主目录性质一样，同样是将宿主文件绑定到容器内部，容器内可以看到这个文件。但是，一旦文件发生改变，情况则有不同。

简单的文件修改，比如 echo "name = jessie" >> myapp.ini，这类修改依旧还是原来的文件，宿主（或容器）对文件进行的改动，另一方是可以看到的。

而复杂的文件操作，比如使用 vim，或者其它编辑器编辑文件，则很有可能会导致一方的修改，另一方看不到。

其原因是这类编辑器在保存文件的时候，经常会采用一种避免写入过程中发生故障而导致文件丢失的策略，既先把内容写到一个新的文件中去，写好了后，再删除旧的文件，然后把新文件改名为旧的文件名，从而完成保存的操作。从这个操作流程可以看出，虽然修改后的文件的名字和过去一样，但对于文件系统而言是一个新的文件了。换句话说，虽然是同名文件，但是旧的文件的 inode 和修改后的文件的 inode 不同。

    $ ls -i
    268541 hello.txt
    $ vi hello.txt
    $ ls -i
    268716 hello.txt

如上面的例子可以看到，经过 vim 编辑文件后，inode 从 268541 变为了 268716，这就是刚才说的，名字还是那个名字，文件已不是原来的文件了。

而 Docker 的 绑定宿主文件，实际上在文件系统眼里，针对的是 inode，而不是文件名。因此容器内所看到的，依旧是之前旧的 inode 对应的那个文件，也就是旧的内容。

这就出现了之前的那个问题，在宿主内修改绑定文件的内容，结果发现容器内看不到改变，其原因就在于宿主的那个文件已不是原来的文件了😂。

这类问题解决办法很简单，如果文件可能改变，**那么就不要绑定宿主文件，而是绑定一个宿主目录，这样只要目录不跑，里面文件爱咋改就咋改😁。**


## 17. 多个 Docker 容器之间共享数据怎么办？NFS ？
如果是同一个宿主，那么可以绑定同一个数据卷，当然，程序上要处理好并发问题。

如果是不同宿主，则可以使用分布式数据卷驱动，让分布在不同宿主的容器都可以访问到的分布式存储的位置。如S3之类：

https://docs.docker.com/engine/extend/plugins/#volume-plugins


## 18. 既然一个容器一个应用，那么我想在该容器中用计划任务 cron 怎么办？
cron 其实是另一个服务了，所以应该另起一个容器来进行，如需访问该应用的数据文件，那么可以共享该应用的数据卷即可。而 cron 的容器中，cron 以前台运行即可。

比如，我们希望有个 python 脚本可以定时执行。那么可以这样构建这个容器。

首先基于 python 的镜像定制：
```
FROM python:3.5.2
ENV TZ=Asia/Shanghai
RUN apt-get update \
    && apt-get install -y cron \
    && apt-get autoremove -y
COPY ./cronpy /etc/cron.d/cronpy
CMD ["cron", "-f"]
```
其中所提及的 cronpy 就是我们需要计划执行的 cron 脚本。

`* * * * * root /app/task.py >> /var/log/task.log 2>&1`
在这个计划中，我们希望定时执行 /app/task.py 文件，日志记录在 /var/log/task.log 中。这个 task.py 是一个非常简单的文件，其内容只是输出个时间而已。
```
#!/usr/local/bin/python
from datetime import datetime
print("Cron job has run at {0} with environment variable ".format(str(datetime.now())))
```
这 task.py 可以在构建镜像时放进去，也可以挂载宿主目录。在这里，我以挂载宿主目录举例。
```
# 构建镜像
docker build -t cronjob:latest .
# 运行镜像
docker run \
    --name cronjob \
    -d \
    -v $(pwd)/task.py:/app/task.py \
    -v $(pwd)/log/:/var/log/ \
    cronjob:latest
需要注意的是，应该在构建主机上赋予 task.py 文件可执行权限。
```

## 19. 如何初始化卷？
卷（Volume），是用于动态数据持久化的。因此其内存储的都是动态数据，运行时会变化。如果这里面需要初始化里面的数据，需要在运行时进行。或者在镜像里加入初始化的脚本，比如 mysql 镜像中的初始化目录中的脚本；或者自己单独制作纯粹用于初始化卷用的镜像，单独一次性运行以将初始化数据灌入卷中。

举个例子来说，假设你需要个卷 mydata，然后里面需要有个 hello.txt 文件是必须存在的，否则容器运行就要出大事儿了……（这需求很傻我知道……😅好吧，假设如此）。

当然，我们得先有这个卷。

`docker volume create --name mydata`
那怎么把这个超重要的 hello.txt 文件放入卷中呢？有几种办法。

正常挂载该 mydata 卷，然后 docker cp 进去
这是个很傻的办法，不过如果容器运行并不依赖于 hello.txt 的话，这样做是可以的。
```
$ docker run -d --name web -v mydata:/data nginx
$ docker cp ./hello.txt web:/data/
```
这样是先让容器启动，启动后，再把所需数据导入卷里面去。以后容器就可以使用 /data/hello.txt 文件了。

但是，如果容器是严重依赖于这个 hello.txt 文件的话，这样做就会出问题。容器会因为 hello.txt 文件不存在，而报错退出，导致根本没有 docker cp 的机会。

这种情况，我们可以变通一下。
```
$ docker run --rm \
    -v $PWD:/source \
    -v mydata:/data \
    busybox \
    cp /source/hello.txt /data/

$ docker run -d --name web -v mydata:/data nginx
```
这里我们先启动了一个 busybox 容器，分别挂载要复制的源以及目标的 mydata 卷，然后用 cp 命令将 hello.txt 复制到 mydata 中去。数据导入结束后，我们再正式挂载 mydata 卷到正式的容器上并启动。这个时候严重依赖 /data/hello.txt 的这个容器就可以顺利运行了。

专门制作初始化镜像
手动的去执行 docker cp，或者 docker run ... cp ... 并不是很正规。可以写个脚本让一切都标准化，但是，除了流程外，还需要确保当前环境中的初始化数据的版本必须是所期望的，否则初始化了错误的数据，也会让运行时状态达不到预期的效果。

因此，另一种办法是专门制作一个初始化卷的镜像，这样的做法也比较方便在 CI/CD 流程中对初始化数据的过程进行测试确认。
```
FROM busybox
COPY hello.txt /source/
VOLUME /data
CMD ["cp", "/source/hello.txt", "/data/"]
```
这样的镜像只有一个生存目的，就是挂载 mydata 卷，并且把数据导入进去。假设构建好的镜像名为 volume-prepare，只需要执行下面的命令就可以完成导入：

`$ docker run --rm -v mydata:/data volume-prepare`
在镜像的 Dockerfile 制作中，加入初始化部分
在之前的问答中我们已经了解到，官方镜像 mysql 中可以使用 Dockerfile 来添加初始化脚本，并且会在运行时判断是否为第一次运行，如果确实需要初始化，则执行定制的初始化脚本。

我们也可以使用这种方法将 hello.txt 在初始化的时候加入到 mydata 卷中去。

首先我们需要写一个进入点的脚本，用以确保在容器执行的时候都会运行，而这个脚本将判断是否需要数据初始化，并且进行初始化操作。
```
#!/bin/bash
# entrypoint.sh
if [ ! -f "/data/hello.txt" ]; then
    cp /source/hello.txt /data/
fi
exec "$@"
```
名为 entrypoint.sh 的这个脚本很简单，判断一下 /data/hello.txt 是否存在，如果不存在就需要初始化。初始化行为也很简单，将实现准备好的 /source/hello.txt 复制到 /data/ 目录中去，以完成初始化。程序的最后，将执行送入的命令。

我们可以这样写 Dockerfile：
```
FROM nginx
COPY hello.txt /source/
COPY entrypoint.sh /
VOLUME /data
ENTRYPOINT ["/entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
```
当我们构建镜像、启动容器后，就会发现 /data 目录下已经存在了 hello.txt 文件了，初始化成功了。

## 20. 如何列出容器和所使用的卷的关系？
要感谢强大的 Go Template，可以使用下面的命令来显示：
```
docker inspect --format '{{.Name}} => {{with .Mounts}}{{range .}}
    {{.Name}},{{end}}{{end}}' $(docker ps -aq)
```
注意这里的换行和空格是有意如此的，这样就可以再返回结果控制缩进格式。其结果将是如下形式：
```
$ docker inspect --format '{{.Name}} => {{with .Mounts}}{{range .}}
    {{.Name}}{{end}}{{end}}' $(docker ps -aq)
/device_api_1 =>
/device_dashboard-debug_1 =>
/device_redis_1 =>
    device_redis-data
/device_mongo_1 =>
    device_mongo-data
    61453e46c3409f42e938324d7feffc6aeb6b7ce16d2080566e3b128c910c9570
/prometheus_prometheus_1 =>
    fc0185ed3fc637295de810efaff7333e8ff2f6050d7f9368a22e19fb2c1e3c3f
```

## 21.那我把所有命令都合并到一个 RUN 就对了吧？
不是把所有命令都合为一个 RUN，要合理分层，以加快构建和部署。

**合理分层就是将具有不同变更频繁程度的层，进行拆分，让稳定的部分在基础，更容易变更的部分在表层，使得资源可以重复利用，以增加构建和部署的速度。**

以 node.js 的应用示例镜像为例，其中的复制应用和安装依赖的部分，如果都合并一起，会写成这样：
```
COPY . /usr/src/app
RUN npm install
```
但是，在 node.js 应用镜像示例中，则是这么写的：
```
COPY package.json /usr/src/app/
RUN npm install
COPY . /usr/src/app
```
从层数上看，确实多了一层。但实际上，这三行分开是故意这样做的，其目的就是合理分层，充分利用 Docker 分层存储的概念，以增加构建、部署的效率。

在 docker build 的构建过程中，如果某层之前构建过，而且该层未发生改变的情况下，那么 docker 就会直接使用缓存，不会重复构建。因此，合理分层，充分利用缓存，会显著加速构建速度。

第一行的目的是将 package.json 复制到应用目录，而不是整个应用代码目录。这样只有 pakcage.json 发生改变后，才会触发第二行 RUN npm install。而只要 package.json 没有变化，那么应用的代码改变就不会引发 npm install，只会引发第三行的 COPY . /usr/src/app，从而加快构建速度。

而如果按照前面所提到的，合并为两层，那么任何代码改变，都会触发 RUN npm install，从而浪费大量的带宽和时间。

合理分层除了可以加快构建外，还可以加快部署，要知道，docker pull 的时候，是分层下载的，并且已存在的层就不会重复下载。

比如，这里的 RUN npm install 这一层，往往会几百 MB 甚至上 GB。而在 package.json 未发生变更的情况下，那么只有 COPY . /usr/src/app 这一层会被重新构建，并且也只有这一层会在各个节点 docker pull 的过程中重新下载，往往这一层的代码量只有几十 MB，甚至更小。这对于大规模的并行部署中，所节约的东西向流量是非常显著的。特别是敏捷开发环境中，代码变更的频繁度要比依赖变更的频繁度高很多，每次重复下载依赖，会导致不必要的流量和时间上的浪费。


## 22. context 到底是一个什么概念？
context，上下文，是 docker build 中很重要的一个概念。构建镜像必须指定 context：

docker build -t xxx <context路径>
或者 docker-compose.yml 中的
```
app:
    build:
        context: <context路径>
        dockerfile: dockerfile
```
这里都需要指定 context。

context 是工作目录，但不要和构建镜像的Dockerfile 中的 WORKDIR 弄混，context 是 docker build 命令的工作目录。

docker build 命令实际上是客户端，真正构建镜像并非由该命令直接完成。docker build 命令将 context 的目录上传给 Docker 引擎，由它负责制作镜像。

在 Dockerfile 中如果写 COPY ./package.json /app/ 这种命令，实际的意思并不是指执行 docker build 所在的目录下的 package.json，也不是指 Dockerfile 所在目录下的 package.json，而是指 context 目录下的 package.json。

这就是为什么有人发现 COPY ../package.json /app 或者 COPY /opt/xxxx /app 无法工作的原因，因为它们都在 context 之外，如果真正需要，应该将它们复制到 context 目录下再操作。

话说，有一些网文甚至搞笑的说要把 Dockerfile 放到磁盘根目录，才能构建如何如何。这都是对 context 完全不了解的表现。想象一下把整个磁盘几十个 GB当做上下文发送给 dockerd 引擎的情况，😱……

**docker build -t xxx . 中的这个.，实际上就是在指定 Context 的目录，而并非是指定 Dockerfile 所在目录。**

默认情况下，如果不额外指定 Dockerfile 的话，会将 Context 下的名为 Dockerfile 的文件作为 Dockerfile。所以很多人会混淆，认为这个 . 是在说 Dockerfile 的位置，其实不然。

一般项目中，Dockerfile 可能被放置于两个位置。

一个可能是放置于项目顶级目录，这样的好处是在顶级目录构建时，项目所有内容都在上下文内，方便构建；
另一个做法是，将所有 Docker 相关的内容集中于某个目录，比如 docker 目录，里面包含所有不同分支的 Dockerfile，以及 docker-compose.yml 类的文件、entrypoint 的脚本等等。这种情况的上下文所在目录不再是 Dockerfile 所在目录了，因此需要注意指定上下文的位置。
此外，项目中可能会包含一些构建不需要的文件，这些文件不应该被发送给 dockerd 引擎，但是它们处于上下文目录下，这种情况，我们需要使用 .dockerignore 文件来过滤不必要的内容。.dockerignore 文件应该放置于上下文顶级目录下，内容格式和 .gitignore 一样。
```
tmp
db
```
这样就过滤了 tmp 和 db 目录，它们不会被作为上下文的一部分发给 dockerd 引擎。

**如果你发现你的 docker build 需要发送庞大的 Context 的时候，就需要来检查是不是 .dockerignore 忘了撰写，或者忘了过滤某些东西了。**

## 23. ENTRYPOINT 和 CMD 到底有什么不同？
Dockerfile 的目的是制作镜像，换句话说，实际上是准备的是主进程运行环境。那么准备好后，需要执行一个程序才可以启动主进程，而启动的办法就是调用 ENTRYPOINT，并且把 CMD 作为参数传进去运行。也就是下面的概念：

`ENTRYPOINT "CMD"`
假设有个 myubuntu 镜像 ENTRYPOINT 是 sh -c，而我们 docker run -it myubuntu uname -a。那么 uname -a 就是运行时指定的 CMD，那么 Docker 实际运行的就是结合起来的结果：

`sh -c "uname -a"`
如果没有指定 ENTRYPOINT，那么就只执行 CMD；
如果指定了 ENTRYPOINT 而没有指定 CMD，自然执行 ENTRYPOINT;
如果 ENTRYPOINT 和 CMD 都指定了，那么就如同上面所述，执行 ENTRYPOINT "CMD"；
如果没有指定 ENTRYPOINT，而 CMD 用的是上述那种 shell 命令的形式，则自动使用 sh -c 作为 ENTRYPOINT。
注意最后一点的区别，这个区别导致了同样的命令放到 CMD 和 ENTRYPOINT 下效果不同，因此有可能放在 ENTRYPOINT 下的同样的命令，由于需要 tty 而运行时忘记了给（比如忘记了docker-compose.yml 的 tty:true）导致运行失败。

这种用法可以很灵活，比如我们做个 git 镜像，可以把 git 命令指定为 ENTRYPOINT，这样我们在 docker run 的时候，直接跟子命令即可。比如 docker run git log 就是显示日志。

## 24. 应用代码是应该挂载宿主目录还是放入镜像内？
两种方法都可以。

如果代码变动非常频繁，比如开发阶段，代码几乎每几分钟就需要变动调试，这种情况可以使用 --volume 挂载宿主目录的办法。这样不用每次构建新镜像，直接再次运行就可以加载最新代码，甚至有些工具可以观察文件变化从而动态加载，这样可以提高开发效率。

如果代码和配置文件没有那么频繁变动，比如发布阶段，这种情况，应该将构建好的应用放入镜像。

**需要注意的一点是，绑定宿主目录虽然方便，但是不利于集群部署，因为集群部署前还需要确保集群各个节点同步存在所挂载的目录及其内容。因此集群部署更倾向于将应用打入镜像，方便部署。**

## 25. 为什么在 Dockerfile 中执行（导入 .sql、service xxx start）不管用？
这是典型的对 Dockerfile 以及镜像、容器的基本概念不了解。

Dockerfile 不是 shell 脚本，而是定制 rootfs 的脚本。它并不是在运行时运行的，而是在构建时运行的。

导入 .sql 文件到数据库，实际上修改的是数据库数据文件，而数据库的数据文件存储于卷，默认为匿名卷，因此当导入行为结束后，构建该层的容器停止运行，匿名卷被抛弃，所有导入行为都会丢失，因此所谓的导入 .sql 的行为在 Dockerfile 里实际上完全没有意义。

而 service xxxx start 也完全没有意义，这是启动后台服务，且不说 Docker 中不用后台服务，这种启动行为对文件系统根本没影响，这仅仅是让后台在构建所用的容器中运行一下，完全没有意义。最后运行容器的时候，是另一个进程了，该没启动的东西还是不会启动。

但是不要因此就盲目的得出 Dockerfile 无法初始化数据库的结论。所有官方镜像都考虑到了定制的问题，去看特定官方镜像的文档，基本都会看到定制、初始化的方法。

比如官方 mysql 镜像中，可以把初始化的 .sql 脚本文件在 Dockerfile 中 COPY 至 /docker-entrypoint-initdb.d/ 目录中，在容器第一次运行的时候，如果所挂载的卷是空的，那么就会依次执行该目录中的文件，从而完成数据库初始化、导入等功能。
```
FROM mysql:5.7
COPY mysql-data-backup.sql /docker-entrypoint-initdb.d/
```

## 26.Docker 日志都在哪里？怎么收集？
日志分两类，一类是 Docker 引擎日志；另一类是 容器日志
Docker 引擎日志
Docker 引擎日志 一般是交给了 Upstart(Ubuntu 14.04) 或者 systemd (CentOS 7, Ubuntu 16.04)。前者一般位于 /var/log/upstart/docker.log 下，后者一般通过 jounarlctl -u docker 来读取。

## 27.都说不要用 root 去运行服务，但我看到的 Dockerfile 都是用 root 去运行，这不安全吧？
并非所有官方镜像的 Dockerfile 都是用 root 用户去执行的。比如 mysql 镜像的执行身份就是 mysql 用户；redis 镜像的服务运行用户就是 redis；mongo 镜像内的服务执行身份是 mongo 用户；jenkins 镜像内是 jenkins 用户启动服务等等。所以说 “都是用 root 去运行” 是不客观的。

当然，这并不是说在容器内使用 root 就非常危险。容器内的 root 和宿主上的 root 不同，容器内的 root 虽然 uid 也默认为 0，但是却处于一个隔离的命名空间，而且被去掉了大量的特权。容器内的 root 是一个没有什么特权的用户，危险的操作基本都无法执行。

不过，如果用户可以打破这个安全保护，那就是另外一回事了。比如，如果用户挂载了宿主目录给容器，这就是打通了一个容器内的 root 操控宿主的一个通道，使得容器内的 root 可以修改所挂载的目录下的任何文件。

因为当前版本的 Docker 中，默认情况下容器的 user namespace 并未开启，所以容器内的用户和宿主用户共享 uid 空间。容器内的 uid 为 0 的 root，就被系统视为 uid=0 的宿主 root，因此磁盘读写时，具有宿主 root 同等读写权限。这也是为什么一般不推荐挂载宿主目录、特别是挂载宿主系统目录的原因之一。这一切只要定制镜像的时候，容器内不使用 root 启动服务就没这个问题了。

当然，上面说的问题只是默认情况下 user namespace 不会启用的问题。dockerd 有一个 --userns-remap 参数，只要配置了这个参数，就可以确保容器内的 uid 是独立命名空间，容器内的 uid 变到宿主的时候，会被 remap 到另一个范围。因此，容器内的 uid=0 的 root 将完全跟 root 没有任何关系，仅仅是个普通用户而已。

## 28.

## 如何删除私有 registry 中的镜像？
首先，在默认情况下，docker registry 是不允许删除镜像的，需要在配置config.yml中启用：
```
delete:
    enabled: true
```
然后，使用 API GET /v2/<镜像名>/manifests/<tag> 来取得要删除的镜像:Tag所对应的 digest。

Registry 2.3 以后，必须加入头 `Accept: application/vnd.docker.distribution.manifest.v2+json`，否则取到的 digest 是错误的，这是为了防止误删除。

比如，要删除 myimage:latest 镜像，那么取得 digest 的命令是：
```
$ curl --header "Accept: application/vnd.docker.distribution.manifest.v2+json" \
  -I -X HEAD http://192.168.99.100:5000/v2/myimage/manifests/latest \
  | grep Digest
Docker-Content-Digest: sha256:3a07b4e06c73b2e3924008270c7f3c3c6e3f70d4dbb814ad8bff2697123ca33c
```
然后调用 `API DELETE /v2/<镜像名>/manifests/<digest>` 来删除镜像。比如：

`curl  -X DELETE http://192.168.99.100:5000/v2/myimage/manifests/sha256:3a07b4e06c73b2e3924008270c7f3c3c6e3f70d4dbb814ad8bff2697123ca33c`
至此，镜像已从 registry 中标记删除，外界访问 pull 不到了。但是 registry 的本地空间并未释放，需要等待垃圾收集才会释放。而垃圾收集不可以在线进行，必须停止 registry，然后执行。比如，假设 registry 是用 Compose 运行的，那么下面命令用来垃圾收集：
```
docker-compose stop
docker run -it --name gc --rm --volumes-from registry_registry_1 registry:2 garbage-collect /etc/registry/config.yml
docker-compose start
```
其中 registry_registry_1 可以替换为实际的 registry 的容器名，而 /etc/registry/config.yml 则替换为实际的 registry 配置文件路径。

##docker push 了很多镜像到私有的 registry 上，怎么才能查看上面都有啥？或者搜索？
两种办法，一种是使用 Registry V2 API。可以列出所有镜像：

`curl http://<私有registry地址>/v2/_catalog`
如果私有 Registry 尚支持 V1 API（已经废弃），可以使用 docker search

`docker search <私有registry地址>/<关键字>`


##docker push 到私有 registry 总是不成功，怎么办？
如果在报错中看到了 https，那很可能是因为 registry 没有配置证书。

很多人最开始配置 registry 的时候，为了简单而没有配置 TLS 证书。

这是不安全的做法，在 Docker 中不推荐使用。因此，刻意的增加了使用这种不安全 registry 的复杂度。使用者必须在 dockerd 配置中，明确声明要使用这些不安全的 registry。

比如，在 Ubuntu 16.04 中，编辑 /etc/systemd/system/multi-user.target.wants/docker.service 中的 ExecStart= 的结尾，加入 --insecure-registry=192.168.99.100:5000，将 192.168.99.100:5000 替换成你的 registry 地址。如果有很多 registry，可以设置多组。或者如果虚拟机的 IP 总是变化，也可以使用 CIDR 的形式，比如 **--insecure-registry=192.168.99.0/24**。

不过测试过后，一定要配上 TLS 证书。现在 Let’s Encrpyt 已经支持 DNS 认证了，不需要暴露内部的机器于公网，用其脚本自动取得免费证书是很方便的。
获取脚本的网站：https://certbot.eff.org/

##自己架的 registry 怎么任何用户都可以取到镜像？这不安全啊？
那是因为没有加认证，不加认证的意思就是允许任何人访问的。

添加认证有两种方式：

Registry 配置中加入认证： https://docs.docker.com/registry/configuration/#/auth
```
auth:
  token:
    realm: token-realm
    service: token-service
    issuer: registry-token-issuer
    rootcertbundle: /root/certs/bundle
  htpasswd:
    realm: basic-realm
    path: /path/to/htpasswd
```
前端架设 nginx 进行认证：https://docs.docker.com/registry/recipes/nginx/
```
location /v2/ {
    ...
    auth_basic "Registry realm";
    auth_basic_user_file /etc/nginx/conf.d/nginx.htpasswd;
    ...
}
```
