---
title: docker
date: 2022-02-20
tags: [docker]
---

docker的基本应用

<!--more-->

# 1. 概述

- docker是干什么的？

  不会模拟底层硬件的虚拟机。为每个应用提供一个独立的环境。

- Dockerfile、Image 、Container

  - Image：简单理解为虚拟机的快照，里面包含了部署的应用程序和所关联的所有库和软件。通过镜像可以创建很多个容器。
  - 容器：简单理解为运行的虚拟机，里面跑着应用程序
  - dockerfile: 创建镜像的脚本，就像安装虚拟机操作系统

- 安装 -- 按照官方文档操作即可。

- 实例：

  - 新建一个Dockerfile

    - `FROM python:3.8-slim-buster` 基础镜像，可以在docker hub上找一些适合的镜像，可以省去很多复杂的环境配置，有操作系统和应用框架，比如python、redis等

    - `EORKDIR /app` 这个命令**之后**的所有命令的工作目录，如果不存在会自动创建这个目录

    - `COPY <本地路径> <目标路径>`  目标路径是docker镜像中的工作路径，也就是`/app`

    - `RUN pip install -r requirements.txt` 运行shell命令

    - `CMD ["python3", "app.py"]` 容器运行起来之后的所有命令

      `[运行的文件,参数1,参数2...]`

  - `docker build -t my_finance .` `-t`指定名字，`.`表示dockefile路径

  - `docker run -p 80:5000 -d my_finance` 80是本机端口，5000是镜像端口，`-d`是后台运行的意思，不在命令行显示

 # 2. Docker 安装

- Docker基本组成

  - 镜像：Image 模板，可以用这个模板来创建容器服务

    比如 tomcat镜像 -> run  -> tomcat1容器（提供服务器），可以用镜像创建多个容器，最终服务或者项目就是运行在容器之中的

  - 容器：container

    可以简单理解为一个简易的linux系统。

  - 仓库：repository

    存放镜像的地方，公有的比如Docker Hub、阿里云等

  ![image-20220220224829581](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20220220224829581.png)

- 安装Docker

  - 环境准备：Linux 
  - 卸载旧的版本
  - 有多种方式
    - 用仓库 线上的安装
    - 无网络时使用package安装即可
  - 启动并测试HelloWorld

# 3. Run的流程

- 开始
- 在本机寻找镜像，有的话使用这个，没有的话去配置的仓库里下载，若仓库里没有会报错，若有，则下载到本地，并使用这个镜像。

# 4.底层原理

Docker是一个Client-Server结构的，Docker的守护进程运行在主机上，通过Socket客户端访问。

DockerServer接收到Docker-Client的指令，就会执行这个指令。

![image-20220221002554649](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20220221002554649.png)

Dokcer比虚拟机的抽象层少，更快。利用的是宿主机的内核，虚拟机时GuestOS。

# 5. 常用命令

## 帮助

```shell
docker version
docker info
docker 命令 --help # 还可以查官方文档
```

## 镜像命令

**docker images** 查看所有本地主机上的镜像

```shell
docker images #查看主机上所有的镜像
-a 列出所有的镜像
-f 
-q 只显示id
# 使用 --help看帮助就好啦

# 解释
REPOSITORY 镜像的仓库源
TAG 镜像的标签
IMAGE ID 镜像的id
CREATED 镜像的创建时间
SIZE 镜像的大小
```

**docker search**搜索镜像

```shell
# 就像在docker hub上搜索一样的
docker search
--filter=STARS=3000 # 大于3000
```

**docker pull** 下载镜像

```shell
docker pull 名字[:tag]
```

分层下载，相同的可以共用

**docker rmi** 删除镜像

```shell
docker rmi -f id
docker rmi -f id1 id2 id3
docker rmi -f $(docker images -aq)
```

## 容器命令

**说明：有了镜像才可以创建容器**

比如下载一个linux镜像

```shell
docker pull centos
```

这个比虚拟机快多了

**新建容器并启动**

``` shell
docker run [可选参数] image

# 参数说明
--name="Name" 取名字，区分容器
-d 后台方式运行
-it 使用交互方式运行，进入容器内部查看内容
-p 指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口
	-p 容器端口
-P 随机指定端口

docker run -it cnetos /bin/bash # 启动容器并进入容器
里面就是一个小的linux系统，和外部是不一样的
exit 退出容器
```

**列出正在运行的容器**

```shell
docker ps 当前的
-a 列出历史运行过的
-n=N 只列出N个最近的
```

**退出容器**

```shell
exit 停止退出
ctrl + p + q 容器不停止并退出
```

**删除容器**

```shell
docker rm 容器id # 不可以删除正运行的 -f
docker rm -f $(docker ps -aq)
dicker ps -a -q | xargs docker rm 
```

**停止启动**

```shell
docker start 容器id
docker restart id
docker stop id
docker kill id
```

## 常用的其他命令

**后台启动容器**

```shell
docker run -d centos # 使用docker ps时发现停止了 因为docker容器后台运行，就必须有一个前台进程,docker发现没有应用，就会自动停止
```

**日志**

```shell
docker logs -f -t --tail 10 容器id没有日志
# 写一段脚本
docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1; done"

docker ps

docker logs -f -t 容器id --tail 20
-tf 显示日志 t是时间戳
--tail number
```

**查看进程信息**

```shell
docker top 容器id
```

**查看镜像元数据 **

```shell
docker inspect 容器id
```

**进入当前正在运行的容器**

```shell
# 通常使用后台运行，进入容器
docker exec -it id /bin/bash # 打开一个新的终端
# 或者
docker attach id # 正在执行当前的代码
```

**拷贝命令:容器到主机**

```shell
docker cp 容器id:容器内路径:主机路径
# 未来可以使用数据卷 来实现主机文件和docker容器内部文件的同步
```

## Docker 命令小结

todo

---

## 实战： 部署nginx环境

- 搜索镜像

  ```shell
  docker search nginx
  ```

- 拉取

  ```shell
  docker pull nginx
  docker images
  ```

- 启动

  ```shell
  docker run -d --name nginx01 -p 3344:80
  # 测试
  curl localhost:3344 
  ```

## 实战： 部署tomcat环境

```shell
docker run -it --rm tomcat:9.0
# 用完就删掉了，官方提供的方法，一般用于测试
docker pull tomcat
docker run -d -p 3355:8080 --name tomcat01
docker extc -ittomcat01 /bin/bash # 进去发现是一个阉割版的版本：少了linux命令，没有webapps, 因为阿里云默认是最小的镜像，所有不要的都剔除掉了
```

我们以后要部署项目，每次都要进入容器是不是十分麻烦？

## 实战: 部署es+kibana

es暴露的端口很多，十分耗内存，数据要挂在到安全目录

```shell 
docker run -d --name es01 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
# 启动之后 linux卡死了，因为es非常耗费内存
docker stats 
# 测试一下es是否成功了
curl localhost:9200
# 关掉es 增加内存限制 -e 修改环境配置
docker stop es-id
docker run -d --name es01 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xms" elasticsearch:7.6.2
```

