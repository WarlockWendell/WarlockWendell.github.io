---
title: Python拾遗
date: 2021-12-14
tags: [Python]
---

生成器 包和模块一些基础知识  argparse模块



<!--more-->

## 1 生成器

### 概念

是一种特殊的迭代器，所以拥有迭代器的特征

- 惰性计算数据，节省内存
- 能够记录状态，并通过`next()`函数访问下一个状态
- 具备可迭代也行

但是，如果想要打造一个自己的迭代器会比较复杂，需要实现`__iter__`和`__next__`方法， 所以就有一个更加优雅的方式**生成器**

### 创建方式

- 生成器的表达式

  将列表的推导式的中括号改成小括号

  ```python
  l = [i for i in range(1,10000) if i%2 == 0]
  ```

  ```python
  l = (i for i in range(1,1000) if i%2==0)
  ```

  第二个就是一个生成器

- 生成器函数

  包含`yield`, 函数的执行的结果就是一个生成器（一个特殊迭代器）

  每次`next`到`yield`返回一次，而且会继续往下执行，执行完会抛出一个`StopIteration`异常

### 产生数据的方式

生成器具备可迭代特性

- `next()`函数 等价于 `生成器.__next__()`
- `for in `方法

### send()函数

- 有一个参数，指定的是上一次被挂起的`yield`的返回值

  ```python
  def test():
      res1 = yield 1
      print(res1)
      
      res2 = yield 2
      print(res2)
  g = test()
  print(next(g)) # 不打印
  print(g.send('oooo')) # 将res1 = ’oooo' 所以会打印'oooo'
  ```

  

### 关闭生成器

- `close`方法
- `StopIteration`

### 注意

- 碰到`return`怎么办？

  碰到`return`立马抛出`StopIteration`异常

- 使用两次生成器可以吗？

  ```python
  g = test()
  for i in g:
      print(i)
  for i in g:
      print(i)
  ```

  只能迭代一次，想使用再创建一次就好了

## 2 异常处理

- 常见的系统异常：

  都是继承自`BaseException`

  - 除以零异常:  `1/0` `ZeroDivisionError`

  - 名称异常: `NameError`

  - 类型异常:`'1'+2` `TypeError`

  - 索引异常: `IndexError`

  - 键异常:`KeyError`

  - 值异常: `ValueErro`

  - 属性异常: `AttributeError`

  - 迭代器异常: `StopIteration`

  - 系统异常类继承树：

    ![image-20211215113055118](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211215113055118.png)

- 如何处理异常？

  - 预防异常：

    避免触发异常

    添加一些容错代码，但是可能代码太多，逻辑混乱了

  - 解决异常：

    可能会触发时，需要手动处理异常

    - 捕捉异常

      ```python
      try:
          print(name)
      except NameError:
          print('名称有问题，请检查')
      ```

      这样可以保证程序不会崩溃

    - 捕获的完整语法

      ```python
      try:
          代码 从上往下检测，只会检测一个异常，运行完然后就会跳出这一块了
      except 异常 as e: (可以有多个重复)
          处理异常
      else:
          没有异常时处理
      finally:
          不管有无异常都会运行的代码
      ```

      ```python
      try:
          1/0
          print(name) # 只会捕获到第一个除以零异常
      except ZeroDivisionError as e:
          print('xxx',e)
      except NameError as e:
          print('ooo',e)
      else: # 必须放在except后面 可以不写
          print(123)
      finally: # 可以不写 必须放在最后
          print(345)
      ```

    - 异常的合并处理

      ```python
      try:
          1/0
          print(name) # 只会捕获到第一个除以零异常
      except (ZeroDivisionError,NameError) as e:
          print('xxx',e)
      else: # 必须放在except后面 可以不写
          print(123)
      finally: # 可以不写 必须放在最后
          print(345)
      ```

    - 不知道抛出啥异常可以写`Exception`类

      ```python
      try:
          1/0
          print(name) # 只会捕获到第一个除以零异常
      except Exception as e:
          print('xxx',e)
      else: # 必须放在except后面 可以不写
          print(123)
      finally: # 可以不写 必须放在最后
          print(345)
      ```

    方案2：

    执行一段代码A之前进行预处理， 执行结束后，进行清理操作， 不管出现了啥异常， 最终都要执行一些清理操作

    语法

    ```python
    with context_experssion [as target(s)]:
        with_body
    ```

    `context_experssion`上下文表达式：返回一个上下文管理器的对象（实现了`__enter__`和`__exit__`的对象）

    流程：

    - 上下文表达式返回一个上下文管理对象
    - 调用对象的`__enter__`方法，并 将返回值赋值给`target`(如果有的话)
    - 执行语句体
    - 执行上下文管理器的`__exit__`方法

    ```python
    with open('xxx.txt','rb') as f:
        pass
    ```

    `open(file)` 会返回一个上下文管理器，实现了`__enter__`（返回一个文件对象）和`__exit__`(包含关闭文件)

    - 自定义一个上下文管理器

      ```python
      class Test:
          def __enter__(self):
              print('enter')
              return 'xxx'
          def __exit__(self,exc_type, exc_val, exc_tb):
              print('exit')
              return True # 返回True异常不往外抛
      with Test() as t:
          pass
      ```

      - `exc_type` : 异常类型
      - `exc_val` 异常值
      - `exc_tb` 追踪信息 `traceback` 错误定位

      ```python
      import traceback
      class Test:
          def __enter__(self):
              print('enter')
              return 'xxx'
          def __exit__(self,exc_type, exc_val, exc_tb):
              print(traceback,extract_tb(exc_tb))
              # 上面可以提取追踪信息
              print('exit')
              return True # 返回True异常不往外抛
      with Test() as t:
          pass
      ```

      异常信息会被抛给`__exit__`方法处理，返回True不会进一步将异常外抛

      可以分离逻辑代码和异常处理代码

    - `contextlib` 模块

      - 将生成器快速变成上下文管理器

        ```python
        import contextlib
        @contextlib.contextmanager
        def text():
            print(1)
            yield 'xxx'
            print(2)
        ```

        `yield`前面是`__enter__`, 下面是`__exit__` 生成的是`__enter__`返回值

        可以快速实现一个上下文管理器，可以很好实现异常捕获

        实例

        ```python
        @contextlib.contextmanager
        def ze():
            try:
                yield
            except ZeroDivisionException as e:
                print(e)
        with ze():
            1 / 0
        ```

      - `closing` 将实现了`close`方法的对象变成上下文管理器

        ```python
        
        class Test:
            def t(self):
                print('t')
            def close(self):
                print('释放')
            def __enter__(self):
                return self
           	def __exit(self, exc_type, exc_val, exc_tb):
                self.close()
        with Test() as t_obj:
            t_obj.t()
        ```

        ```python
        import contextlib
        class Test:
            def t(self):
                print('t')
            def close(self):
                print('释放')
            
        with contextlib.closing(Test()) as t_obj:
            t_obj.t()
        ```

        - `nested` : python2.7之前多个上下文管理器的嵌套， 现在应该不用了吧

          ```python 
          with open('xx.jpg','rb') as from_file:
              with open('xx2.jpg','wb') as to_file:
                  from_contents = from_file.read()
                  to_file.writer(from_contents)
          # python3
          with open('xx.jpg','rb') as from_file, open('xx2.jpg','wb') as to_file:
              from_contents = from_file.read()
              to_file.writer(from_contents)
          # python2.7 之前 就不看了
          ```

  - 手动抛出异常

    - `raise`语句就好了

  - 自定义异常

    自己建的异常尽可能继承自`Exception`

    ```python
    class LessZero(Exception):
        def __init__(self,msg,ec):
           	self.msg = msg
        	self.ec = ec
    	# 下面这个也可以写一下
        def __str__(self):
            return self.msg + self.ec
    ```

## 3 包和模块

- 内置的包和模块

  系统自动`import`的包和模块，就是`__builtin__`模块，想看就用`dir(__builtin__)`

  Python3之后叫做`dir(__builtins__)`

  这些常用的每次都要导入就太复杂了

### 3.1 一般操作

#### 创建

- 模块就是一个`.py`文件
- 包： 就是一个文件夹， 创建一个`__init__.py`文件（Python3.3之后就不用创建了，但是为了兼容性还是创建一下比较好) 导入的时候自动执行这个文件
- 包可以嵌套

#### 基本信息

- 名称：就是文件（夹）名称，不要后缀
- 存放位置`os.__file__` 就是存放位置
- 查看包和模块里的内容`dir(模块/包名称)`

#### 导入

- `import`
  - `import M`

  - `import M1, M2`

  - `import M as m`

    > 如果导入的是一个包，那么默认不会导入任何模块，应该在`__init__.py`文件中（导入包的时候会自动执行`__init__`，再次导入所需要的模块或者使用`from`,

- `from语句`：

  - `from A import B [as C]`

  - `from 模块 import *` : 从模块批量导入资源

    `*`表示非下划线开头的资源

  - `from 包 import *` 从包里导入资源（`__init__.py`里面的`__all__`列表里的东西

- 导入之后到底做了什么事情：

  ![image-20211215192754194](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211215192754194.png)

  ```python
  def test():
      import os
      pass
  ```

  只能在`test`函数的命名空间里使用

-  模块的检索路径

  ![image-20211215193825681](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211215193825681.png)


#### 安装三方模块

- 带有`setup.py` 文件的python setup.py install` 可以离线安装带有`setup.py的三方包， 这个前提是安装过`setuptools` （一样的安装方式，在Python包的网站上下载下来，然后安装就完事了

  安装过程中可能还有其他的依赖需要安装，所以断网可能有的模块安装不了

-  `pip`的使用

  - 看官方文档就完事了

  - 切换安装源

    ```shell
    pip install --index-url https://pypi.douban.com/simple/request
    ```

    永久修改

    ![image-20211215205604871](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211215205604871.png)

    

  - 查看包

    - `pip list`所有包
    - `pip list --not-required`
    - `pip list --outdated`
    - `pip show xxx` 查看信息

  - 搜索包

    - `pip search xxx`
    - `pip search -i 检索地址 xxx`

  - 安装特定版本

    - `pip install 'request==2.18'`
    - `pip install 'request >2,0, <3.0'`

  - 升级包

    - `pip install --upgrade xxx`

  - 卸载包

    - `pip uninstall xxx`

  - 生成冻结需求的文本

    - `pip freeze > requirements.txt`

  - 根据这个文本来安装

    - `pip install -r requirements.txt`

- 补充

  - 三方包的版本命名方式`n1.n2.n3`
    - n3: 修bug
    - n2: 小功能
    - n1: 修改了功能，或者添加了一个新功能（修改了之前的api)

#### 发布一个自己的包（这个感觉暂时用不上）

参照文档做就完事了

#### 更多相关的补充

- 区分模块的测试与发布状态

  可能在包里写了一些测试代码，但是如果用户导入这个包的时候，就会执行这些测试代码呀，这样会有问题的

  解决方案：

  - 发布之前删除测试代码，这样不好，这样我每次都写测试代码，太麻烦了

  - 借助`__name__ == '__main__'`

    当被导入被动执行时，模块的`__name__`就是模块的名称呀，当被直接执行的时候，模块的`__name__`是`__main__`

    这样可以让测试代码在特定的场景下才会执行呀

    包的入口文件`__main__.py`

#### argparse模块

- 命令行参数解析模块

- 使用方法

  - 构造解析器
  - 添加参数
  - 解析参数

- 例子

  ```python
  import argparse
  parse  = argparse.ArgumentParser(description='caculateing the area of a rectangle')
  
  parser.add_argument('--length',type=int, default=10,help='length of the rect')
  parser.add_argument('--width',type=int, default=2,help='width of the rect')
  
  args = parser.parse_args()
  
  if __name__ == '__main__':
      result = args.length * args.width
      print('result',str(result))
  ```

  然后在shell里运行

  ```bash
  python ex.py
  python ex.py -h
  
  python ex.py --length=20 --width=10
  ```



