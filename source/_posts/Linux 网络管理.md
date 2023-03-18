---
title: Linux 网络管理
date: 2020-07-20
tags: [Linux]
---

有关Linux的FTP、NFS、SSH的知识

<!--more-->

# 1. 查看网络相关信息

## 1.1 ifconfig

直接用这个命令可以查看到网络相关信息，下面是我的WSL使用这个命令查看到的一部分信息，下面以这个信息为例解读一下。

```bash
wendell@DESKTOP-R43K3TI:/mnt/e$ ifconfig
eth3      Link encap:Ethernet  HWaddr 00:50:56:c0:00:01
          inet addr:192.168.47.1  Bcast:192.168.47.255  Mask:255.255.255.0
          inet6 addr: fe80::eddb:b4dd:6c4a:810b/64 Scope:Unknown
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Unknown
          UP LOOPBACK RUNNING  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

- `eth3`：网卡，0代表第一块网卡
- `HWaddr`：网卡的MAC地址
- `inet addr`：IP地址
- `bcast`：在摸一个网段内的广播地址，这里是40网段
- `lo`：本地回环

## 1.2 ping

- ping一下IP，但是LInux下直接使用这个会一直ping，windows下是蹦4条

- `-c`：这个参数指定蹦几条

  ```bash
  ping 192.168.47.109 -c 4
  ```

- 可以ping网址

  ```bash
  ping www.baidu.com
  ```

## 1.3 nslookup

可以用这个命令查看网址的相关信息

```bash
nslookup www.baidu.com
```

---

# 2. FTP服务器的搭建

## 2.1 vsftpd

- 在使用前需要手动安装这个软件

  ```bash
  sudo apt install vsftpd
  ```

  安装之后会发现使用`vim /etc/passwd`查看所有用户时，多了一个`ftp`用户

- FTP服务器功能单一，负责文件的上传和下载

- 服务器端

  - 修改配置文件

    - ```bash
      sudo vim /etc/vsftpd.conf
      ```

    - ```bash
      anonymous_enable=YES  #允许匿名用户登录
      local_enable=YES  #允许本地用户登录
      write_enable=YES #实名用户拥有写权限，可以上传文件
      local_umask=022 #设置本地掩码为022
      anon_upload_enable=YES #匿名用户可以向服务器上传文件
      anon_mkdir_write_enable=YES #匿名用户可以穿件一个新目录
      ```

    - 自己看说明文档全部配置好就可以了

  - 重启服务

    ```bash
    sudo service vsftpd restart
    ```

- 客户端

  - 实名用户登录：登陆之后可以在FTP服务器进行操作

    ```bash
    ftp IP(server)
    输入用户名(server)
    输入密码(server)
    ```
    
  - 匿名用户登录：这个才是常用的

    ```bash
ftp ServerIP
    用户名：anonymous
    密码:直接回车
    ```
    
    只能在一个指定的目录下操作，这个目录就是匿名用户的根目录，在配置文件中设置
    
    ```bash
    sudo vim /etc/vsftpd.conf
    ```
    
    加上一句，指定一下目录，比如
    
    ```bash
    anon_root=/home/wendell/path
    ```
    
    重启服务
    
    ```bash
    sudo service vsftpd restart
    ```
    
  - 上传文件：将本地文件上传到，上传的是登录时的路径下的文件，上传到的地方就是你登陆之后所在的目录
  
    ```bash
    put pathname
    ```
  
  - 下载文件：不允许操作目录，如果想操作目录需要先打包再下载
    
    ```bash
    get filename
    ```
    
  - 退出
  
    `quit` `bye` `exit`均可

## 2.2 lftp

- 安装

  ```bash
  sudo apt-get install lftp
  ```

- 登录服务器

  - 匿名登录

    ```bash
    lftp serverIP
    login
    ```

  - 实名登录

    ```bash
    lftp username@IP
    password
    ```

- 使用lftp登录成功后可以使用`lpwd`查看当前本地机器所在目录，使用`lcd`可以切换目录，比vsftpd方便一点
- 使用`mput`可以同时上传多个文件，使用`mget`下载多个文件
- `mirror -R` 上传整个目录
- `mirror`下载整个目录
- 注意：lftp只是一个客户端小工具

---

# 3. NFS服务器的搭建

- 安装

  ```bash
  sudo apt-get install nfs-kernel-server
  ```

- 创建一个共享出去的目录（有点类似于Windows下的共享文件夹）

  ```bash
  mkdir dir
  ```

  修改配置文件

  ```bash
  sudo vim /etc/exports
  ```

  加上自己共享的目录即可

  ```bash
  pathname *(rw,sync) #这个*表示网段，rw表示可读可写，ro是只读，sync表示是实时更新
  ```

  重启

  ```bash
  sudo service nfs-kernel-server restart
  ```

- 客户端：访问，使用挂载的方式访问

  ```bash
  mount serverIP:pathname pathname
  ```

  第一个pathname是要共享的目录，第二个是要挂载到本地的哪个目录下面

---

# 4. SSH服务器

- 服务器端

  - 安装

    ```bash
    sudo apt-get install openssh-server
    ```

- 客户端

  - 登录

    ```bash
    ssh username@IP
    ```

- 退出 `logout`

---

# 5. scp命令

- 这个命令需要先安装SSH服务器

- 拷贝到本地

  ```bash
  scp -r 目标用户名@目标用户IP:目录 本地目录
  ```

- 拷贝到远程

  ```bash
  scp -r 本地目录 目标用户名@目标用户IP:目录
  ```

  