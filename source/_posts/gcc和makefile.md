---
title: Gcc和Makefile
date: 2020-07-10
tags: [Gcc,C/C++]
---

在Linux终端下写代码，没有像VS这种的集成开发环境，就需要使用Gcc和Makefile来执行编译。

<!--more-->

# 0. 前言

现在我使用Linux写代码时，编译单个文件使用的是

```bash
gcc hello.c -o hello
```

会得到一个hello文件（可执行程序）（.elf文件，也就是Win下的.exe文件)

如果编译多文件，要使用如下命令

```bash
gcc a.c b.c c.c -o out
```

如果每次编译写这么多，太麻烦了，所以需要掌握Makefile编写代码

---

# 1. C/C++程序编译过程

- C语言

  - 预处理: 将.h 和 .c文件展开（宏定义替换、头文件粘贴复制） 由`.c`文件到`.i`文件

    ```bash
    gcc -E a.c -o a.i
    ```

    还可指定头文件的目录，使用`-I`

    ```bash
    gcc -E -I ./head a.c -o a.i
    ```

  - 编译: 由每个 `.i`文件生成一个`.s`文件

    ```bash
    gcc -S a.i -o a.s
    ```

  - 汇编: 由每个`.s`文件生成一个`.o`的目标文件

    ```bash
    gcc -c a.s -o a.o
    ```

    或者使用as命令

    ```bash
    as a.s -o a.o
    ```
  
- 链接: 由这么多个目标文件链接成为一个可执行文件
  
    ```bash
    gcc hello.o -o hello
    ```
  
    或者使用ld
  
    ```bash
    ld a.o b.o -o out
    ```
  
  上面就是C语言的编译的微观过程了
  
- C++

  C++的编译过程和C语言是类似的，这里不做赘述
  
- 编译过程中加入宏，使用`-D`

    ```bash
    gcc hello.c -o out -I ./head -D DEBUG
    ```

- 使用`-O`参数优化程序，优化等级为0~3，依次递增

    ```bash
    gcc hello,c -o out -I ./head -D DEBUG -O2
    ```

- `-Wall` ：输出所有的警示

- `-g`：使用gdb调试时加入一些调试信息

---

# 2. Makefile文件

大型C/C++项目开发一定是要用Makefile的

## 2.1 基础知识

- 新建文本文档，取名为Makefile

- `#` : 注释

- 显式规则

  - 语法规则

    ```makefile
    目标文件:依赖文件
    [TAB]指令
    ```
    第一个目标文件是我的最终目标，所以要反着写makefile

  
  ````makefile
    hello:hello.o
    	gcc hello.o -o hello
    hello.o:hello.s
    	gcc -c hello.s -o hello.o
    hello.s:hello.i
    	gcc -S hello.i -o hello.s
    hello.i:hello.c
    	gcc -E hello.c -o hello.i
  ````
  
  使用`make`执行
  
- 伪目标: 不需要目标文件的指令
  
    加上下面的代码可以执行清除
  
    ```makefile
    .PHONY:
    clear： #这个名字是任意取的，在后续调用时使用
    	rm -rf hello.o hello.s hello.i
    ```
  
    在执行是使用`make clear`可以执行伪目标，不加`clear`就不执行

## 2.2 变量

- `=` 替换

- `+=` 追加

- `:=` 恒等于

  ```makefile
  TAR = hello
  OBJ = hello1.o hello2.o
  CC := gcc
  
  $(TAR):$(OBJ)
  	$(CC) $(OBJ) -o $(TAR)
  
  hello1.o:hello1.c
  	$(CC) -c hello1.c -o hello1.o
  hello2.o:hello2.c
  	$(CC) -c hello2.c -o hello2.o
  
  .PHONY:
  cleanall:
  	rm -rf $(OBJ) $(TAR)
  clean:
  	rm -rf $(OBJ)
  ```

## 2.3 隐含规则

- `%.c` `%.o` 通配符，任意的单个文件

- `*.c` `*.o` 所有的文件

  ```makefile
  TAR = hello
  OBJ = hello1.o hello2.o
  CC := gcc
  
  $(TAR):$(OBJ)
  	$(CC) $(OBJ) -o $(TAR)
  
  %.o:%.c
  	$(CC) -c %.c -o %.o
  
  .PHONY:aq
  cleanall:
  	rm -rf $(OBJ) $(TAR)
  clean:
  	rm -rf $(OBJ)
  ```

- `$^` 所有的依赖文件

- `$@` 所有的目标文件

- `$<` 所有的依赖文件中的第一个文件

- `$*` 不包括扩展名的目标文件名称

- `$+` 所有的依赖文件，以空格分开，并以出现的先后为序，可能包含重复的依赖文件

- `$?` 所有时间戳比目标文件晚的依赖文件，以空格分开

  ```makefile
  TAR = hello
  OBJ = hello1.o hello2.o
  CC := gcc
  
  $(TAR):$(OBJ)
  	$(CC) $^ -o $@
  
  %.o:%.c
  	$(CC) -c $^ -o $@
  
  .PHONY:aq
  cleanall:
  	rm -rf $(OBJ) $(TAR)
  clean:
  	rm -rf $(OBJ)
  ```

  makefile文件中的内容还有很多，比如函数一类的，详细还是参见说明文档
