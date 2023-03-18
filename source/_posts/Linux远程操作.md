---
title: Linux远程操作
date: 2019-9-14 06:32:11
tags: [Linux,ssh]
---

在很多时候需要使用其他物理机来远程操作另一台Linux主机，这个时候就需要使用Linux的远程操作命令

<!--more-->

## 1. SSH远程连接

> SSH是Secure Shell的缩写，是一种较为可靠的，专为远程登录会话和其他网络服务提供安全性的协议。SSH的客户端可以在几乎所有类Unix平台上使用

### 1.1 安装SSH

对于ubuntu系统来说，使用如下的命令就可以开启SSH连接

```bash
sudo apt-get install openssh-client
sudo apt-get install openssh-server
```

安装好之后可以使用如下命令来确定是否开启ssh-server

``` bash
ps -e | grep ssh
```

当输出结果出现`sshd` 的进程时，表示ssh服务已经开启，如果没有，使用下面的命令可以启动

```bash
sudo /etc/init.d/ssh start
```

同理可以使用类似命令停止和重启ssh服务

```bash
sudo /etc/init.d/ssh stop
sudo /etc/init.d/ssh restart
```

### 1.2 SSH远程登录

#### 1.2.1 口令登录

```bash
ssh name@ip_addr #或者下面的
ssh -l name ip_addr
```

使用上述命令就可以登录了

- `-X`：调用图形界面
- `-p`：指定端口登录

#### 1.2.2 秘钥登录

这种登录方式是更为方便的，不用每次登录都输入密码

这种方法的步骤如下：

- 首先在被连接的Linux主机上输入`ssh-keygen -t rsa` ，生成秘钥，这里的`-t rsa`表示使用`rsa`的加密算法
- 然后在当前用户的家目录下可以看到一个`.ssh`文件夹，下面有私钥文件`id_rsa` 和公钥文件`id_rsa.pb`
- 将公钥文件的内容复制到远程操作的主机的`~/.ssh/authorized_key`文件中，可以使用复制，也可以使用命令`ssh-copy-id name@ip_addr`来实现这个功能

### 1.3 远程传输文件

使用`scp`文件可以实现远程操作时的文件拷贝功能

将本地文件拷贝到远程

```bash
scp filename RemoteUsername@RemoteIP:RemoteFile
scp filename RemoteIP:RemoteFolder
scp filename RemoteIP:RemoteFile
```

远程文件拷贝到本地

```bash
scp RemoteUsername@RemoteIP:RemoteFile filename
scp RemoteIP:RemoteFile Filename
```

> 注意：当传输文件改成目录时，使用 `-r` 参数



<font size=2>参考[Jack LDZ的博文：SSH简介及两种远程登录的方法](https://blog.csdn.net/li528405176/article/details/82810342)</font>





