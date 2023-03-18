---
title: Linux 进程管理
date: 2020-07-20
tags: [Linux]
---

本文主要记录Linux进程管理的一些概念和命令。

<!--more-->

# 1. 终端

Linux下有7个终端 tty1 - tty7，tty7是图形界面终端，tty1 - tty6都是命令行终端。

- 在不同终端之间切换使用命令

  ```bash
  ctrl + alt + F1~F7
  ```



# 2. 进程

## 2.1 ps

- `-a` : 列出当前所有的用户

- `-u` ：列出详细的信息

- `-x`：列出不带终端的即不需要与用户进行交互的应用程序的信息

- 一般使用时都是使用`-aux`同时使用，然后使用管道进行筛选

  ```bash
  ps aux | grep bash
  ```

## 2.2 kill

- `-9`： 强制杀死进程

  ```bash
  kill -9 PID
  ```

## 2.3 env

查看当前进程下的所有环境变量，也是经常配合管道使用

> Linux下的环境变量
>
> 格式：key = value
>
> value可以有很多个，中间使用`:`间隔
>
> key = value : value : value : value
>
> ```bash
> PATH=/home/wendell/bin:/home/wendell/.local/bin:/usr/local/sbin:/usr/local/bin
> ```
>
> 上面是我的WSL中的部分环境变量

## 2.4 top

相当于WIndows中的任务管理器，是动态的，但是只能看，不能终止，使用`ctrl + c`退出查看。