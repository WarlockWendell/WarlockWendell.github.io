---
title: Python基础教程-面向对象
date: 2021-12-07
tags: [Python]
---

面向对象里的一些知识点

<!--more-->

# 8. Python 面向对象课程

## 8.1 属性

- 经典类、新式类

- 创建对象时的底层操作

  - ```python
    class Money:
        pass
    one = Money()
    ```

  - 类的定义也会占用内存，所以会开辟一块空间给类，类对象，类名(Money)引用着这个类对象，所以类名既是类对象的一个属性，也是一个变量，（所以可以通过给**作为变量的类名**赋值来覆盖类对象的定义）

  - 产生对象时，会根据类对象的定义返回一个实例化对象，赋值给变量`one`

    ![image-20211127120703618](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211127120703618.png)

  属性和变量的区别就是是否有宿主，比如上图中，`Money`和`one`没有宿主，他们是变量名，`__name__`和`__class__`有宿主，分别为类对象和实例对象，他们是属性，进一步划分，类对象的属性称之为类属性，实例对象的属性称为对象属性（其实感觉这个名字不太好，叫实例属性好一点，但为了一致性，还是按课程中名字记录）

### 8.1.1 对象属性

- 宿主是实例化对象的属性叫对象属性

- `name.__dict__`可以查看对象的全部属性字典

- 属性的增加

  - 动态特性添加： `实例对象.属性 = 值`
  - 通过类的初始化方法添加（构造函数）：`__init__`方法

  比如给上面的图中的**实例化对象**添加一个属性`one.age = 18`

  ![image-20211127122053572](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211127122053572.png)

- 属性的访问

  `one.age`

- 属性的修改

  `one.age = 20`

  会新开辟一块空间给20，然后原来指向18的现在指向20

  **如果修改的是一个可变类型，比如列表、字典、集合之类的内部 元素时，地址是不变的，但是要是直接给它换了个对象，就会变。也就是，`p.l = [1,2]`,`p.l[0] = 2`不会变地址，但是`p.l=[2,2]`就会变地址 **

  >这里是说`p.l`的地址是不变的，不是说`p.l[0]`不变，注意，还有一点，这个以前也没有注意到过，应该是和底层实现有关
  >
  >```python
  >l=[1,2]
  >l=[1,2] # id会变
  >
  >l[1] = 2 # l[1]的id不变
  >l[1] = 3 # l[1]的id会变
  ># 这两个操作l的id都不变
  >```

- 删除属性

  `del one.age`

- 补充

  - `__dict__`返回这个对象的所有属性字典

### 8.1.2 类属性

类的定义本身其实也是一个对象,所以它也是有属性的

- 增加属性

  方法1:

  ```python
  class Money:
      pass
  one = Money()
  # one.age = 18 # 实例对象的属性
  Money.count = 1 # 类对象的属性
  print(Money.__dict__) # 不仅包含刚才创建的,还包含它自带的一些属性,实例对象就不会打印这么多属性,因为它们没那么多自带属性
  ```

  ![image-20211127185751662](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211127185751662.png)

  方法2:

  ```python
  class Money:
      age = 18
      count = 1
      num = 666
  ```

  在定义类的时候就指定这些属性

  是不可以通过实例化对象来增加属性的

- 属性的查询

  - 方法1 `Money.count`的方式

  - 方法2 通过实例对象来访问

    ```python
    one = Money()
    # one.__class__这个的结果就是Money这个类的实体
    one.__class__,count # 肯定是可以的
    one.count #这样也是可以的,神奇
    ```

    为什么可以呢? `count`是类对象的属性,又不是`one`这个实例对象的属性

    其实和python的查找机制有关 **简化版的查找流程**

    优先从自己身上去找,找到就直接返回了,.如果没找到,就会通过`__class__`这个属性去找到对应的类对象,再去看看有没有这个属性

    ![image-20211127190821453](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211127190821453.png)

    把这个对象的`__class__`属性改掉,他就访问不到`count`了

    ```python
    class T:
        sex = '男'
    one.__class__ = T
    one.count #会报错啦
    one.sex # 不会报错
    ```

    <img src="https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211127191113024.png" alt="image-20211127191113024" style="zoom: 67%;" />

    如果我们在实例对象里再定义了一个`sex`.那么python就会在实例对象中找到`sex`属性了,就直接返回了,而不会再通过`__class__`方法去查找属性里的方法

- 属性的修改

  - 通过类名修改

    ```python
    Money.count = 2
    ```

  - 通过实例化对象修改是不可以的

    下面这种写法是**错误**的

    ```python 
    one = Money()
    one.count = 3
    ```

    因为这样是直接给实例化对象也增加了一个`count`属性

    **只有查询才是实例对象里找不到再往上找,赋值的话实例对象里找不到直接就新增了**

- 属性的删除

  `del`语句,**只可以删除直系属性,不往上查找**

  `del Money.count`

**只有查询会通过实例对象往上查，不可以通过实例对象增加、删除、修改**

- 属性的内存存储问题

  这个是比较正确的，上面的是方便理解的

  ![image-20211127193339629](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211127193339629.png)

  比如，我可以直接这么写：

  ```python
  one = Money()
  one.__dict__ = {'age':18,'height':180}
  one.__dict__['age'] = 20
  ```

  所以说实际查找变量，还是查`__dict__`这个字典

  **但是类对象里的`__dict__`属性是只读的，不可以用上面这个方法修改**

  ```python
  Money.__dict__ = {'sex':'男'}
  Money.__dict__['name'] = 'sz'
  ```

  上面这两种修改方法都是不可行的

  ```python 
  Money.age = 18
  __setattr__等方法是可以达到间接修改类对象的`__dict__`的目的的，但是不可以直接修改
  ```

- 类属性被各个实例对象所共享

- 如何限制一个对象添加属性呢？

  > 不限制的话，同一个类对象的不同实例化对象的属性可能完全不同，这样使用起来不是很方便呀

  ```python
  class Person:
      __slots__ = ["age"]
      pass
  p1 = Person()
  p1.age = 1
  p1.num = 2 # 会报错
  ```

## 8.2 方法

方法和函数的主要区别是：**调用方式不一样**

### 8.2.1 方法的划分

- 实例方法

  - 接收的第一个参数默认是一个实例

- 类方法

  - 接收到的第一个参数默认是一个类

- 静态方法

  - 第一个参数啥也不默认接收

  例子

  ```python
  class Person:
      def eat(self): # self想怎么写就怎么写，常用self而已
          print('this is a instance method')
      @classmethod
      def method1(cls):
          print('this is a class method')
      
      @staticmethod
      def method2():
          print('this is a static method')
  p = Person()
  p.eat()
  Person.method1()
  Person.method2()
  ```

  

- 注意

  - 划分依据：方法的参数的第一个参数所接受的数据类型

  - 不管是哪一种类型的方法，都是存储在类当中的， 不是存储在实例当中的，也是放在`__dict__`里面

    > 为什么`__dict__`可以放函数呢？
    >
    > 放的是函数对象指针，Python的万物都是对象，函数是对象，属性值也是对象

  - 不同类型的方法的调用方式不同，这是解释器给我们处理的，最红要保证不同类型的方法第一个参数接收到的数据，是他们想要的类型

### 8.2.2 实例方法

- 例子

  ```python
  class Person:
      def eat(self,food):
          print(f'在吃饭{food}')
  p = Person()
  p.eat('apple') # 标准调用
  ```

  > 拿实例调用方法的时候，解释器会自动传递自身作为第一个参数
  >
  > ```python
  > class Person:
  >     def eat():
  >         pass
  > p = Person()
  > p.eat() # 这样是不可以的
  > Person.eat() # 这样是可以的
  > ```
  >
  > 

- 其他调用方法

  - 使用类调用 访问类的`__dict__`

    ```python
    Person.eat(p,'apple')
    Person.eat(123,'apple') # 这里self是 123这个int对象,倒也不必是Person对象
    ```

  - 间接调用

    ```python
    fun = Person.eat
    fun(123,'apple')
    ```

### 8.2.3 类方法

```python
class Person:
    def run(self):
        pass
   	@classmethod
    def fun(cls):
        print(cls)
Person.fun()
p = Person()
p.fun()
func = Person.fun
func()
```

可以通过类和实例两种方法来调用

- 通过类方法调用，将这个类传递给第一个参数

- 通过实例调用，将这个实例对应的类传递给第一个参数

- 通过子类调用，将这个子类传递给第一个参数

  ```python
  class Person:
      @classmethod
      def fun(cls):
          print(cls)
  class A(Person):
      pass
  a = A()
  a.fun()# 结果是<class '__main__,A'>
  ```

### 8.2.4 静态方法

```python
class Person:
    @staticmethod
    def fun():
        print('this is a static method')
Person.fun()
p = Person()
p.fun()
func = Perosn.fun
func()
```

静态方法没有第一个默认参数

可以使用实例调用、类调用和间接调用的访问方式

## 8.3 元类

- Python万物皆对象，所以类也是对象，那么类的`__class__`是什么呢？

  ```python
  num = 10
  print(num.__class__)
  print(num.__class__.__class__) # type
  print(int.__class__) # type
  class Person:
      pass
  print(Person.__class__) # type
  ```

- 这个类就是`type`类，也就是元类，元类是创建类对象的类

  ![image-20211210124014059](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211210124014059.png)

  > ```python
  > print(type.__class__) #依然是type， type已经到了头了
  > ```
  >
  > 

- 类对象的创建方式

  下面这种相当于算是语法糖了

  ```python
  class Person:
      count = 0
      def run(self):
          pass
  # 上面这种只是一种表象，当需要动态创建类的时候，需要其他方式
  ```

  手动创建类

  使用元类创建对象

  `type(类名字:str,base:Tuple, dict:Dict{})`

  ```python 
  def run(self):
      pass
  xxx = type('Dog',(),{'count':0,'run':run})
  d = xxx()
  d.run()
  ```

  ![image-20211210124712437](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211210124712437.png)

  > 这图分别对应两种创建方式

- 元类的查找机制

  `__metaclass__`: 如果对象类指定了元类，就按照元类的方式创建，没创建就找父类的元类，父类没有就找模块的元类，模块再没有就找到`type`元类了

  ```python
  class Person:
      __metaclass__ = xxx
      pass
  class Person(metaclass=xxx):
      pass
  ```

  > 上面两种方式指定元类都是可以的

  ```python
  class Animal:
      __metaclass__ = xxx
      pass
  class Person(Animal):
      pass
  ```

  Person没找到，找Animal

  ```python
  __metaclass__ = xxx
  class Animal:
      pass
  class Person(Animal):
      pass
  ```

  Animal也没有，找模块

  ```python
  class Animal:
      pass
  class Person(Animal):
      pass
  ```

  模块也没有，就用type创建

- 元类的应用场景

  - 拦截类的创建
  - 修改类
  - 返回修改好的类
  - 等等

## 8.4 类的描述

```python
class Person:
    count = 1
    def run(self):
        print('人在跑')
```

- 给类加标准的注释方法

```python
class Person:
    """
    关于这个类的描述，类的作用，类的构造函数，类的属性的描述等等
    """
    count = 1
    def run(self,distance,step):
        """
        PyCharm这里就会自动补全格式了，只用补全
        :param distance: 参数的含义，类型，默认值
        :param step: 参数的含义，类型，默认值
        :return: 返回结果的含义，返回数据的类型
        """
        print('人在跑')
```

```python
help(Person) # 可以查看文档
```

- 注释文档的生成：

  - 方式1：使用内置模块`pydoc`

    `python3 -m pydoc -h ` 查看帮助文档

    - 查看文档描述：`python3 -m pydoc modulename` (注意模块名不需要加上`.py`)

    - 启动本地服务，浏览文档：`python3 -m pydoc -p 6666`

      `python3 -m pydoc -b`自动检测空闲端口

      通过网页查看，用这种方法看开源项目也是可以的呀，很方便，但这种方式会把内建模块也显示出来，会显示所有的模块，所以这个没有模块名

    - 生成指定模块的html文档:`python3 -m pydoc -w modulenanme`

    - 更多使用方法看帮助文档呀

  - 方式2:  使用第三方模块来使用，看相关的帮助文档即可

    - Sphinx
    - epydoc
    - doxygen

    功能和`pydoc`类似的，使用方法略有不同

## 8.5 更多属性相关的

### 8.5.1 私有化属性

- 私有化属性：只有类的内部可以访问，类外面无法访问，**保证数据的安全性**

  > Python没有真正的私有化支持，但是可以使用`_`来完成伪私有
  >
  > `__` 私有的属性
  >
  > `_` 受保护的属性
  >
  > 类属性（方法）和实例属性（方法）遵循相同的规则

  - 类内部可以访问

  - 子类内部**不可以访问**，甚至不可以强行访问

  - 在本模块的其他位置也**不可以访问**

  - 跨模块可以访问

    - `import`可以访问

    - `from 模块 import *`不可以访问

      > 但是模块中指明了 `__all__ = ['__y']` ，使用这种方式是可以访问到`_y`的，而且没有任何警告

  - 实现机制：

    **名字重整机制**，重改`__x`为`_类名__x`，所以直接用`__x`访问不了，但是可以用重整之后的名字来访问，所以是伪私有

    目的：防止外界直接访问，防止被子类同名称属性覆盖

  - 应用场景

    - 数据保护: 不能直接访问

      ```  python
      class Person:
          def __init__(self):
              self.__age = 18
      p1 = Person()
      p1.__age = -10
      print(p1.__dict__)
      ```

      结果肯定不是私有属性被修改了，而是由于名字重整，`__age`变成了`_Person__age`，所以找不到`__age`，所以新建了一个`__age`作为实例属性了

      `{'_Person__age':18,'__age':-10}`

      通过`get` `set`方法来做封装

      ```python
      class Person:
          def __init__(self):
              self.__age = 18
          def set_age(self,age):
              if isinstance(age,int) and 0 < age < 200:
                  self.__age = age
              else:
                  print('Input Error')
          def get_age(self):
              return self.__age
                  
      ```

    - 数据过滤


### 8.5.2 公有属性

- 公有属性：

  - 类内部访问
  - 子类内部访问
  - 模块内其他位置访问
    - 类访问
    - 实例访问s
  - 跨模块访问
    - `import`
    - `from 模块 import *` 形式导入


### 8.5.3 受保护的属性

- 受保护的属性: `_y`

  - 类内部可以访问

  - 子类内部可以访问

  - 模块内部的其他位置可以强行访问，但是不建议这么访问（会有警告）

  - 跨模块也可以强行访问，但是不建议这么访问（会有警告）

    - `import`可以访问

    - `from 模块 import *`不可以访问，这样不会导入`_y`这个变量

      > 但是模块中指明了 `__all__ = ['_y']` ，使用这种方式是可以访问到`_y`的，而且没有任何警告

- 小结：公有属性啥地方都可以访问，私有属性和受保护的属性在跨模块访问时表现出的性质是一样的（`import`可以，`from`需要一些操作），私有属性不可以在子类和模块内其他位置访问，受保护的属性可以在子类访问，可以在模块里的其他位置强行访问

- 补充

  - `xx_`规范的问题，和系统关键字做区分
  - `__xx__` 系统内置的写法，自己命名尽量避免这种命名


### 8.5.4 只读属性的实现

- 只读属性

  - 一个属性（一般是实例属性），只能读取，不能写入

  - 应用场景：有些属性，只限在内部葛奴不同场景进行修改，而对于外界来说，不能修改，只能读取，比如对于电脑类来说的网络状态属性、网速属性

  - 实现方法：

    - 方式1：私有化属性，只有`get`方法就只有读了（部分公开）

      优化：要让只读属性可以用`p1.age`这种方式直接获取，当`p.age = 1`这种赋值操作的时候，应该报错，而不是直接新增了额外的属性

      ```python
      class Person:
          def __init__(self):
              self.__age = 18
              
          # 可以以使用属性的方式，来使用这个方法
          @property
          def get_age(self):
              return self.__age
      p1 = Person()
      print(p1.get_age)
      ```

      将`get_age`修改成`age`,更爽了，这样就像是一个属性了

      使用这种方式也可以避免`p1.get_age = 1`，这样写会报错

      **@property** 主要作用是使用操作属性的方式，来使用一个方法，严格的定义是：将一些‘属性的操作方法’（删、改、查）关联到某一个属性中

      `property(fget,fset,fdel,doc=None) -> property attribute` ，当想要读取时，调用`fget`方法，设置时调用`fset`方法, 删除时调用`fdel`方法， 详细的可以查看文档呀

      ![image-20211211123244731](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211211123244731.png)

      概念补充

      - 经典类：没有继承自object的类

      - 新式类：继承自object的类

        ```python
        class Person(object):
            pass
        class Dog:
            pass
        print(Person.__bases__)
        print(Dog.__bases__)
        ```

        python3的默认隐式都是继承自object的，所以上面两个都是新式类

        (<type 'object'>,) , 但是python2里面，只有显式地继承自object才是新式类

        建议显式地继承自object，为了兼容性

      property在经典类和新式类中式有不同的方式

      ```python
      class Person(object):
          def __init__(self):
              self.__age = 18
          def get_age(self):
              return self.__age
          def set_age(slef,value):
              self.__age = value
          age = property(get_age,set_age) #这样写是可以的，其实也是很难想象这是啥语法，为啥python还可以在类里这么干
      ```

      ```python
      class Person(object):
          def __init__(self):
              self.__age = 18
          @property
          def age(self):
              return self.__age
          @age.setter
          def age(self,value):
              self.__age = value
       
      ```

      > `__init__`方法，有点像C++的构造函数，因为在没有这个方法的时候，我们总是需要构造出一个实例对象`p1 = Person()`,然后再给这个实例属性`p1.age = 18`,当一个对象的属性太多了之后，这个就是很麻烦的事情了，所以使用这个函数来给这个实例对象一些默认属性，或者说，当一个对象被创建的时候，就自动调用了`__init__()`这个函数，所以你看相当于自动执行了`self.age=18`(这里的self是p1这个实例对象呀)，妙不可言呀。这样索然是在类里面写的，但是这个`age`还不是类属性，是属于这个实例的实力属性

      在经典类中的使用方式(在python2中才有)
    
      ```python
      class Person:  
          def __init__(self):
              self.__age = 18
          def get_age(self):
              return self.__age
          def set_age(slef,value):
              self.__age = value
          age = property(get_age,set_age)
      # 这里的 p.age get是可以的，但是set是不可以的 
      ```
    
      ```python
      class Person(object):
          def __init__(self):
              self.__age = 18
          @property
          def age(self):
              return self.__age
          @age.setter
          def age(self,value):
              self.__age = value
       # 这里的 p.age get是可以的，但是set是不可以的  
      ```

      经典类里**只能关联读取方法，不能关联其他方法**

    - 方式2：直接操作私有属性的重整时候的名字可以改，通过`__dict__`字典也是可以直接修改的，怎么屏蔽这种修改方式呢,就用更底层的方法来实现这个就好啦
    
      ```python
      class Person:
          
          # 在我们通过实例.属性 = 值， 给一个实例增加一个属性，或者修改一个属性得时候，都会自动调用这个方法，在这个方法的内部，才会真正的把这个属性以及对应的数据给存储到 __dict__字典里面
          def __setattr__(self,key,value):
              print(key,value)
              # 判定key是否要被屏蔽
              if key == 'age' and key in self.__dict__.keys():
                  print('这个属性只读，不可以被修改')
              else:
                  self.__dict__[key] = value
      ```
    
      > 这种方式好像是针对实例属性的？针对类属性应该怎么做呢？ 好像也可以用类似的方法吧，用类方法不就完事了嘛哈哈哈
      >
      > ```python
      > class Person:
      > @classmethod
      > def __setattr__(cls,key,value):
      >   print(key,value)
      >   # 判定key是否要被屏蔽
      >   if key == 'count' and key in cls.__dict__.keys():
      >       print('这个属性只读，不可以被修改')
      >   else:
      >       cls.__dict__[key] = value
      > ```
      >
      > 事实证明，这样是**不可以的**，那么一个类属性的修改过程是啥样的呢，暂时还不知道，只知道实例属性是`__setattr__`,需要知道类属性在创建时，调用了哪个方法
      >
      > 应该是调用了元类的`__setattr__`方法吧，更深的细节我也挖不出来了，调用的是`type`的`__setattr__`方法
      >
      > 那我做如下修改是不是也可以呢？
      >
      > ```python
      > class mytype(type):
      >  def __setattr__(self,key,value):
      >       print(key,value)
      > class Person(metaclass=mytype):
      >  pass
      > Person.count = 9
      > ```
      >
      > 发现果然是调用了元类的`__setattr__`的，于是想着用如下修改实现只读变量
      >
      > ```python
      > class mytype(type):
      >  def __setattr__(self,key,value):
      >      if key == 'count' and key in self.__dict__.keys():
      >          print('这个属性只读，不可以被修改')
      >      else:
      >          self.__dict__[key] = value
      > class Person(metaclass=mytype):
      >  pass
      > Person.count = 9
      > ```
      >
      > 结果显然是不仅可以的，因为类对象的`__dict__`是只读的，不可以通过`self.__dict__[key] = value`这个来修改呀，前面学到的是可以通过`__setattr__`这个修改，所以这不就陷入了僵局了
      >
      > 虽然没能解决问题，但是还是收获到了一些东西的，比如上面这个东西的实现，都是通过在模块类里操作实现的，实例对象的模板是类对象，所以要实现实例对象的只读属性，要在类里操作，类对象的模板是元类，所以顺利成章应该是在元类里操作的，只不过现在理解还不够深，不能窥探其中更深层次的东西，有点可惜。


### 8.5.5 内置的特殊属性

-   内置特殊属性

  - 类属性
    - `__dict__`：类里的属性列表
    - `__bases__` ：说明可以多继承
    - `__doc__` ： 类的文档字符串
    - `__name__` ： 类的名字
    - `__module__`: 类定义在的模块
  - 实例属性
    - `__dict__`：实例的属性列表（不包含类里定义的）
    - `__class__`：实例所对应的类

  ​    

## 8.6 更多方法相关的

- 方法和属性本质上都是一样的，都是对象，将指针存储在`__dict__`那里
- 私有方法：`def __run()`这样就变成了私有化的方法了 重整成了`_Person__run()`了

### 8.6.1 内置的特殊方法

- 内置的特殊方法

  实现了内置方法就可以完成一些特定的功能

  - 生命周期方法，这个留在下一节，因为实在太重要了

  - #### 信息格式化操作

    - `__str__`方法：实现了这个方法可以用`print(p1)`来实现

      ```python
      class Person:
          def __init__(self,age,name):
              self.age = age
              self.name = name
      	def __str__(self):
              return f'name{self.name}, age{self.age}'
      p1 = Person(19,'Wendell')
      print(p1)
      ```

      使用print函数会触发`__str__`方法，如果没有实现，就是找`__repr__`函数了

      使用`str`方法也会触发这个方法，如果没有实现，就是找`__repr__`函数了。

      可以让对象打印更加格式化，更加好看

    - `__repr__`方法：

      和`__str__`大致差不多，但是面向对象不同，`__repr__`函数主要面向开发者，获取到实例的一些本质信息，比如没有实现 ‘'`__str_` 时，会找`__repr__`，这个时候就会打印对象的类型，地址之类的信息 

      而 `__str__`主要面向用户

      ```python
      class Person:
          def __init__(self,age,name):
              self.age = age
              self.name = name
      	def __str__(self):
              return f'name{self.name}, age{self.age}'
          def __repr__(self):
              return 'xxxx'
      p1 = Person(19,'Wendell')
      print(p1)
      print(repr(p1)) # xxxx
      ```

      比如

      ```python
      import datetime
      t = datetime.datetime.now()
      print(t) # 
      print(repr(t))
      ```

      ```python
      2021-12-11 15:19:41.016357
      datetime.datetime(2021, 12, 11, 15, 19, 41, 16357)
      ```

      可见上面时面向用户的，下面时面向程序员的

      调用`__repr__`的场景：

      - 使用`repr()`函数

      - 没有实现`__str__`时调用了`print()`或者`str()`函数时

      - 在交互模式下，直接使用`p`这种形式，就是调用`__repr__`函数，而不是`__str__`函数

        比如

          ```python 
          class A:
              def __str__(self):
                  print('1')
              def __repr__(self):
                  print('2')
          a = A()
          print(a) # 1
          a # 2
          ```

      
      可以使用`eval()`函数将这个信息转换成对象
      
      ```python
      import datetime
      t = datetime.datetime.now()
      result = eval(repr(t))
      print(result)
      ```
      
      这样就是将这个对象的信息变成相应的实际对象呀
    
  - #### 调用操作 `__call__`方法

    使得"对象"具有被调用的能力

    ```python
    class Person:
        def __call__(self,*args,**kwargs):
            print('xxx')
    p = Person()
    p() # xxx
    ```

    是不是很熟悉呢？就是pytorch里的网络里的那些东西呀,只定义了`forward`函数，但是可以通过`net(X)`这种方式前传，`net`本身只是一个对象而已，但是实现了`__call__`方法，让它有了被调用的能力

    > `*args`表示如果参数不带名称就传给它当作一个元组
    >
    > `**kwargs`表示如果指明了名字，传给它当作一个字典
    >
    > ```python
    > class Person:
    >     def __call__(self,*args,**kwargs):
    >         print('xxx',args,kwargs)
    > p = Person()
    > p(1,2,name='sd') # xxx
    > ```
    >
    > ```python
    > xxx (1,2) {'name':'sd'}
    > ```
    >
    > 当然`__call__`方法并没有强行规定一定要这个作为输入，可以根据需求自己定义参数列表的

    应用场景

    比如

    ```python
    class PenFactory:
        def __init__(self,p_type):
            self.p_type = p_type
        def __call__(self,p_color):
            print(f'创建了一个{p_type},颜色是{p_color}')
    penF = penFactory('钢笔')
    penF('红色')
    penF('黄色')
    penF('蓝色')
    pencilF = penFactory('铅笔')
    pencilF('红色')
    pencilF('黄色')
    penF('蓝色')
    ```
    
  - #### 索引操作

    作用：可以让一个对象进行索引操作

    步骤：

    实现三个内置方法：

    - `__setitem__(self,key,value)`

      可以赋值

    - `__getitem__(self,item)`

      可以索引获取值

    - `__delitem__(self,key)`

      可以删除

    ```python
    class Person:
        def __setitem__(self,key,value):
            print('set', key, value)
        def __getitem__(self,item):
            print('get', item)
        def __delitem__(self,key):
            print('del', key)
    p = Person()
    p['name'] = 'sd' # set
    print(p['name']) # get name \nNone
    del p['name'] # del
    ```

    上面是验证我们在索引的时候，到底发生了什么事，现在再来看一下具体应该如何实现

    ```python
    class Person:
        def __init__(self):
            self.cache = {}
        def __setitem__(self,key,value):
            print('set', key, value)
            self.cache[key] = value
        def __getitem__(self,item):
            print('get', item)
            return self.cache[item]
        def __delitem__(self,key):
            print('del', key)
            del self.cache[key]
    p = Person()
    p['name'] = 'sd' # set
    print(p['name']) # get name \n sd
    del p['name'] # del
    ```

    这样就具体实现了呀

  - #### 切片操作

    我们可以对列表进行切片，也希望可以对一个对象实现切片的操作

    和索引是类似的

    python2要求实现`__setspice__`, `__getspice__`和`__delspice__`方法，这些方法现在都过期了

    python3只需要实现和索引相同的3个方法就完事了

    ```python
    class Person:
        def __init__(self):
            self.cache = [1,2,3,4,5,6,7,8]
        def __setitem__(self,key,value):
            print('set', key, value)
            self.cache[key] = value
        def __getitem__(self,item):
            print('get', item)
            return self.cache[item]
        def __delitem__(self,key):
            print('del', key)
            del self.cache[key]
    ```

    不同的是，当做切片操作时，`key`是一个切片对象(`slice`)，有`start` `step` 和`stop`属性

    切片操作只可以修改的，不能新增

    ```python 
    p =Person()
    p[0:4:2] = ['a','b'] # ['a',2,'b',4,5,6,7,8]
    ```

  - #### 比较操作

    作用：可以自定义对象比较大小、相等以及真假的规则，有点像C++的操作符重载方式

    ```python
    class A:
        pass
    a1 = A()
    a2 = A()
    print(a1 > a2) # 不允许这样比较 python2里是比较地址
    ```

    实现对应的方法

    `__eq__(self,other)`:相等 

    ```python
    class A:
        def __init__(self,age,height):
            self.age  = age
            self.height = height
        def __eq__(self,other): # 按照年龄比较
            return self.age == other.age
        pass
    a1 = A(18,180)
    a2 = A(19,190)
    print(a1 == a2)  # 自动嗲用了__eq__方法
    ```

    不相等也会进`__eq__`，当然自己实现也是可以的

    `__ne__` 不相等，实现了这个就不会按照`__eq__`反着来

    `__gt__`大于

    `__lt__`小于

    `__ge__`大于等于

    `__le__`小于等于

    **注意**:如果对于反向操作，如果只定义了其中一种方法，那么解释器会采用调换参数的方式来调用实现的那个方法，如果都实现了那就没事了， **但是不支持叠加操作，比如只实现小于`__lt__`和等于`__eq__`，但是不能直接调用`<=`**

    那怎么让他自动推导出来呢？

    ```python
    import functools
    @functools.total_ordering
    class A:
        def __init__(self,age,height):
            self.age  = age
            self.height = height
        def __eq__(self,other): # 按照年龄比较
            return self.age == other.age
        def __lt__(self,other):
            return self.age < other.age
        pass
    ```

    使用了这个装饰器，根据你写的一些方法自动组合的方法和相反的方法

  - #### 上下文布尔值 `__bool__()`方法

    ```python
    class Person:
        def __bool__(self):
            return Fasle
    p = Person()
    if p:
        print('t')
    else:
        print('f') # 结果是f，因为__bool__返回是False
    ```

    ```python
    class Person:
        def __init__(self,age):
            self.age = age
        def __bool__(self):
            return self.age >= 18
    p = Person(20)
    if p:
        print('t')
    else:
        print('f') 
    ```

  - #### 遍历操作

    怎么让我怎么自己创建的对象可以使用`for in`进行遍历？

    - 方式1：实现`__getitem__(self,idx)`方法

      ```python
      class Person:
          def __init__(self):
              self.result = 1
          def __getitem__(self,item):
              self.result += 1
              if self.result > 10:
                  raise StopIteration('停止遍历')
              return self.result
      p = Person()
      for i in p:
          print(i)      
      ```

      > 要注意种中止的条件呀，for in 循环就是捕捉这个`StopIteration`异常了

    - 方式2：实现`__iter__`方法和`__next__()`方法

      ```python
      class Person:
          def __init__(self):
              self.result = 1
         	def __iter__(self):
              return self # 返回一个迭代器对象就好了,实现了next方法就好了,会不断调用next方法
          def __next__(self): # 所以应该在这里抛出异常了
              self.result += 1
              if self.result > 10:
                  raise StopIteration('停止遍历')
              return self.result
              
      p = Person()
      for i in p:
          print(i)  
      ```

      对于for in 循环来说`__iter__()`的优先级高于`__getitem__()`函数的优先级，所以当都实现了的时候，是调用`__iter__()`函数，而不是`__getitem__()`函数

    > 使用for循环会遍历一个迭代器（不准确，不一定是迭代器），但是你看你实现了`__getitem__`方法，这个就可以进行下去了，下表每次加1就好了
    >
    > 实现了`__iter__()`方法也是可以的，此时会将这个对象变成一个可迭代对象，但是此时还不够，应该这个还不是迭代器，所以需要实现`__next__()`方法，此时就可以用for迭代了，(也可以直接使用`next(p)`这种方式直接调用`__next__()`方法)
    >
    > 可以理解成for循环就是不断地`next(p)`,直到捕获到了`StopIteration()`异常，**`__getitem__()`的实现方法可以理解成语法糖（变成的不是可迭代对象也不是迭代器，但是它可以被for in访问，说明可以被for in访问的也不一定是迭代器）**，Pytorch里的dataloader可以理解成就是用这种语法糖实现的，应为dataloader实现了`__getitem__()`方法就可以迭代了，（但是注意还有`__len__`方法也要实现下）

    判断是否是迭代器: 实现了`__iter__`和`__next__`就是迭代器

    ```python
    import collections
    print(isinstance(p,collections.Iterator)) #True
    ```

    > 可以用`next()`访问的不一定是迭代器，因为只要求实现了`__next__()`方法，未必实现了`__iter__()`方法
    >
    > 只实现了`__iter__()`不是迭代器，但是一个可迭代对象
    >
    > ```python
    > print(isinstance(p,collection.Iterable))
    > ```

    迭代器的复用：

    不复用的时候

    ```python
    for i in p:
        print(i)
    for i in p:
        print(i) # 直接就不执行了，因为没有复用
    ```

    要让他复用，只用回退迭代条件就好了

    ```python
    class Person:
        def __init__(self):
            self.result = 1
       	def __iter__(self):
            self.age = 1
            return self 
        def __next__(self): # 所以应该在这里抛出异常了
            self.result += 1
            if self.result > 10:
                raise StopIteration('停止遍历')
            return self.result
    ```

    这样for循环开始时会调用以下`__iter__`函数，这样就可以重复迭代了

    > 注意：`__getitem__()`实现之后既不是迭代器，也不是可迭代对象，但是可以使用`iter`函数将其变成一个迭代器
    >
    > 实现了`__iter__`和`__next__`之后这个对象本身就是一个迭代器，当然也可以使用`iter`函数变一下，但是不是必要的，但是对于只实现了`__getitem__`的类，只有调用一下才会编程迭代器
    >
    > 使用`iter`函数得到的是`__iter__`的返回值

    `iter`函数还有一种方法（和迭代器没啥关系了就）

    ```python
    iter(callable,stop不包括)
    ```

    比如

    ```python
    p = Person()
    iter(p.__next__,4) # 将返回结果和4比较，否则就一直调用这个函数，所以理论上第一个值是个函数就好了，然后能返回值，可以和后面的值比较 此时会打印出来 2 3
    ```

  - #### 描述器

    概念：可以描述一个属性操作的对象，其**本质也是一个对象**，属性的操作也就增改(`__set__`) 删(`__delete__`) 查(`__get__`)
    有一点像`__setattr__`，但是`__setattr__`是针对一个类的所有的属性而言的，所有属性统一管理，但是描述器是和一个具体的属性绑定的

    那和装饰器那个有啥区别呢？`property`装饰器装饰之后也可以达到对对象的管理呀? 继续看下去发现就是property，只是老师将这些内容混在一起讲了而已

    - 定义方法：

      - 方式1：`property`

        ```python
        class Person:
            def __init__(self):
                self.__age = 18
            
            def get_age(self):
                return self.__age
            def set_age(self,value):
                if value < 0:
                    value = 0
                self.__age = value
            def del_age(self):
                del self.__age
            age = property(get_age, set_age, del_age)
        ```

        可以发现`age`就是一个描述器，是一个对象，描述属性的操作，这也解释了为什么可以这么定义了，是可以在类里定义一个描述器的

        当然也可以使用装饰器来调用

        ```python
        class Person:
            def __init__(self):
                self.__age = 18
            @property
            def age(self):
                return self.__age
            @age.setter
            def age(self,value):
                if value < 0:
                    value = 0
                self.__age = value
            @age.deleter
            def age(self):
                del self.__age
            age = property(get_age, set_age, del_age)
        ```
        
      - 方式2: 实现三个方法
      
        ```python
        class Age:
            def __get__(self,instance,owner):
                pass
            def __set__(self,instance,value):
                pass
            def __delete__(slef,instance):
                pass
        class Person:
            age = Age()
        ```
      
        这样就可以实现了，封装实例方法对应属性的增改 删 查 解释器就会认为这个东西是一个描述器 **一般是通过实例操作，而不是通过类来操作**，即通过`p.age`这种方式操作，而不是`Person.age`这种方式操作，用类的方式操作只能做查找操作，增改和删除都无法调用描述器
      
         描述器失效的地方：
      
        - 在python2中，需要Age和Person都是新式类才可以（即宿主类和描述器类都需要是新式类）
      
        - 方法拦截
      
          一个实例属性的正常访问顺序：
      
          1. 实例对象自身的`__dict__`字典
          2. 对应类对象的`__dict__`字典
          3. 如果有父类，会再往上一层的`__dict__`字典里检测
          4. 如果没找到，又定义了`__getattr__`方法，就会调用这个方法
      
          上面这个流程里，是没有`__get__`方法的插入了
      
          是通过这个方法实现的`__getattribute__`，大致是：如果实现了描述器的`__get__`方法，会直接调用，如果没有实现，就会按照上面的机制去查找
      
          所以去查找一个属性的时候：**先会调用`__getattribute__`**去看有没有实现`__get__`方法，如果没有再按上面四步走（这个方法默认就是这么干的，如果你改写了`__getattribute__`方法，那么事情就会按照你定义的方式来走了）
      
    - 资料描述器 get set都实现了, 非资料描述器 只实现了get方法

      资料描述器的优先级高于实例属性, 非资料描述器的优先级低于实例属性

      ```python
      class Age:
          def __get__(self):
              pass
          def __set__(self,value):
              pass
          def __delete__(self):
              pass
      class Person:
          age = Age()
          def __init__(self):
              self.age = 10
      ```

      像上面这个是资料描述器,此时`self.age`操作的是这个描述器,当只实现了get方法的时候,是非资料描述器,此时操作的就是一个通常的实例属性, 不是一个描述器

    - 数据存储问题:

      数据存储在哪里?  存在实例对象比较好,因为像上面的写法,描述器是一个类属性,那么这个类的所有实例的描述器都是同一个对象,那你就不能把数据绑定到这个对象了,那你把Age对象设置成实例属性,是不是就不一样了?

      No, 发现只有类属性才能这么干?

      ```python
      class Person:
          def __init__(self):
              self.age = Age() 
      ```

      这样修改的时候不是调用描述器了

      > 注意:**虽然是将描述器定义成了一个类对象,但是调用描述器的时候一般都是通过实例对象调用的**,如果是通过类对象调用,只能调用到`__get__`方法
      
    
  - #### 装饰器

    - 先来补充一点有关装饰器的基础知识:

      - 闭包: 函数嵌套情况下, 内层函数调用了外层函数的变量(或者接收到的参数), 并且将整个内层函数作为外层函数的返回,那么将这个内层函数和引用到的变量统称为一个闭包

        ```python
        def test():
            a = 10
            def test2():
                print(a)
            return test2
        newF = test()
        newF()
        ```

      - 应用场景:外层函数根据不同的参数,来生成不同功能的函数

        功能是:

        ```python
        def line_config(content, length):
            print("-"*(length//2) + content + "-"*(length//2))
            pass
        line_config('111',20)
        line_config('111',20)
        line_config('111',20)
        line_config('111',20)
        ```

        用闭包调用的时候不需要每次都传参数:

        ```python
        def line_config(content, length):
            def line():
                print("-"*(length//2) + content + "-"*(length//2))
            return line
        line = line_config('111',20)
        line()
        line()
        line2 = line_config('abc',20)
        line2()
        line2()
        ```

      - 注意事项:

        - 当内部函数**修改**外部函数的变量时,不能直接修改,因为会当作是一个新的变量,需要加上一个`nonlocal`关键字

          ```python
          def test():
              num = 10
              def test2():
                  nonlocal num
                  num = 20
                  print(num)
              return test2
          ```

        - 当在闭包中引用了一个后期会发生变化的变量时,一定要注意

          ```python
          def test():
              funcs = []
              for i in range(1,4):
                  def test2():
                      print(i)
                  funcs.append(test2)
               return funcs
          newF = test()
          nerF[0]()
          newF[1]()
          newF[2]()
          # 结果 3 3 3 不是 1 2 3
          ```

          只有在函数被执行的时候才会去找值到底是啥,解释了为什么函数定义的时候找不到变量名不会报错.到执行的时候才会报错

          那要实现这个咋办呢?

          ```python
          def test():
              funcs = []
              for i in range(1,4):
                  def test2(i):
                      def inner():
                          print(i)
                      return inner()
                  funcs.append(test2(i)) # 追加的是执行后的结果,执行的时候会找到对应的值
              return funcs
          ```

        - python的装饰器

          将函数功能和业务功能分离开

          ```python
          def fashuoshuo():
              print('发说说')
          def fatupian():
              print('发图片')
          btnIndex = 1
          if btnIndex == 1:
              fashuoshuo()
          elif btnIndex == 2:
              fatupian()
          ```

          现在新增一个功能: 要登陆了才可以执行上述操作

          可能就这么写了: 这样是在业务逻辑里添加验证,这样不好,如果很多页面都要跳转到发说说,这样每个逻辑里都要加验证,这样会出问题的: 业务逻辑的代码很多,这样每次都要修改,重用度差,且有很多冗余 (Ver1)

          ```python
          def fashuoshuo():
              print('发说说')
          def fatupian():
              print('发图片')
          def check():
              print('检查登录')
          btnIndex = 1
          if btnIndex == 1:
              check()
              fashuoshuo()
          elif btnIndex == 2:
              check()
              fatupian()
          ```

          所以直接在功能函数里修改会比较好: 重用性和可维护性会比较好 (Ver2)

          ```python
          def fashuoshuo():
              check()
              print('发说说')
          def fatupian():
              check()
              print('发图片')
          def check():
              print('检查登录')
          btnIndex = 1
          if btnIndex == 1:
              fashuoshuo()
          elif btnIndex == 2:
              fatupian()
          ```

          但是这个违背了开放封闭原则: 已经写好的代码,尽可能不要修改,如果想要新增功能,在原先代码基础上,单独进行扩展; 也违背了单一职责原则,这个发说说的代码实现了检查登录和发说说两个功能,这样不太好,这也是在设计中最常见的造成代码难读懂的错误了

          我现在希望业务逻辑代码不改变,也希望能够检查登录,貌似上面这个是最好的方法了,但是它不满足单一职责的原则呀 (ver3 wrong)

          ```python
          def check(func):
              print('检查登录')
              func()
          def fashuoshuo():
              check()
              print('发说说')
          def fatupian():
              check()
              print('发图片')
          fashuoshuo = check(fashuoshuo)
          fatupian = check(fatupian)
          
          btnIndex = 1
          if btnIndex == 1:
              fashuoshuo()
          elif btnIndex == 2:
              fatupian()
          ```

          这样有一些小问题,因为在给函数重新赋值的时候,会执行一遍,这个是不希望的,使用闭包来完成这个功能(Ver4): 这样就完成了功能的更新了,没有改变原先功能函数的函数体,但也给函数新增了一些功能(装饰).所以这种设计模式就称为装饰器设计模式

          ```python
          def check(func):
              def inner():
                  print('检查登录')
                  func()
              return inner
          
          def fashuoshuo():
              check()
              print('发说说')
          def fatupian():
              check()
              print('发图片')
          fashuoshuo = check(fashuoshuo)
          fatupian = check(fatupian)
          
          btnIndex = 1
          if btnIndex == 1:
              fashuoshuo()
          elif btnIndex == 2:
              fatupian()
          ```

          但是这样写不是很好看, python提供了一个语法糖来干这个活(ver5)

          ```python
          def check(func):
              def inner():
                  print('检查登录')
                  func()
              return inner
          
          @check
          def fashuoshuo():
              check()
              print('发说说')
          @check
          def fatupian():
              check()
              print('发图片')
          
          
          btnIndex = 1
          if btnIndex == 1:
              fashuoshuo()
          elif btnIndex == 2:
              fatupian()
          ```

          这样功能和上面这个是一样的

          写了这个`@check`时,写了立即执行,不用等到调用

          写了这个`@check`就相当于写了`fashuoshuo = check(fashuoshuo)`这句话了,装饰了一下这个函数

        - 装饰器的使用注意事项

          - 装饰器叠加原则: 从上到下装饰,从下到上执行

          - 对有参数的函数进行装饰

            ```python
            def zsq(func):
                def inner(n):
                    print('_'*30)
                    func(n)
                return inner
            @zsq
            def pnum(num):
                print(num)
            ```

            理解本质了这个就好理解了

            `@zsq`干的事是`pnum = zsq(pnum)`, 返回的是一个函数,现在用户调用的时候肯定还是希望有一个参数`num`的,所以这个`inner`函数一定也是要接收一个参数的,不然就会出错了,所以这么干就没啥问题了,本质上就是将函数修改了一下呀, 想要调用方式不变,那函数的声明要保持一致

            但是有问题呀,如果希望传递的参数是不定长的呢?

            ```python
            def zsq(func):
                def inner(*args,**kwargs):
                    print('_'*30)
                    func(*args,**kwargs) # 拆包
                return inner
            @zsq
            def pnum(num):
                print(num)
            @zsq
            def pnum2(num1,num2):
                print(num1, num2)
            ```

          - 有返回值的函数怎么装饰:

            本质上就是将函数改成了inner函数呀,所以你希望装饰成啥样,你就把inner函数写成啥样呀

            ```python
            def zsq(func):
                def inner(*args,**kwargs):
                    print('_'*30)
                    res = func(*args,**kwargs) # 拆包
                    return res
                return inner
            @zsq
            def pnum(num):
                print(num)
                return -num
            @zsq
            def pnum2(num1,num2):
                print(num1, num2)
            ```

            上面这个算是一个比较通用的模板了

          - 让装饰本身也是可以变的: 带有参数的装饰器

            ```python
            def zsq(func):
                def inner():
                    print('_'*30)
                    func()
                return innner
            @zsq
            def f1():
                print('666')    
            ```

            定义一个函数可以获取装饰器: 本质: 将`@`符号后面一坨当作装饰器,来装饰后面的函数就好了

            ```python
            def getzsq(char):
                def zsq(func):
                    def inner():
                        print(char * 30)
                        func()
                    return inner
                return zsq
            @getzsq('_')
            def f1():
                print('sdfs')
            @getzsq('*')
            def f2():
                print('qwr')
            ```

            `@`后面的`getzsq('-')`这一坨当作一个装饰器,事实证明它返回的确实是一个装饰器

    - 装饰器的类实现

      ```python 
      def check(func):
          def inner():
              print('登录验证')
              func()
          return inner
      @check
      def fashuoshuo():
          print('发说说')
      ```

      怎么使用一个类来做一个装饰器: 回想一下装饰器的本质是 `fashuoshuo = check(fashuoshuo)`, 如果这里的`check`是一个类的话,就会调用类的初始化方法, 然后我在调用的时候是`fashuoshuo()`等价于`check(fashuoshuo)()` 即调用了一个实例对象,这个时候会调用`__call__`方法

      ```python
      class check:
          def __init__(self,func):
              self.f = func
              pass
          def __call__(self,*args, **kwargs):
              print('登陆验证')
              self.f()
              pass
      @check
      def fashuoshuo():
          print('发说说')
      ```

## 8.7 Python对象的生命周期

- 指的是一个对象从诞生到消亡的过程,当一个对象被创建时,会在内存中分配相应的内存空间进行存储,当这个对象不再使用的时候,就会把这个对象释放掉
- 如何去监听一个对象的生命过程? Python如何去掌控一个对象的生命的?

### 8.7.1 监听对象生命周期

- `__new__`方法: 当我们创一个对象的时候,通过这个方法给对象分配内存,通过拦截这个方法,可以修改对象的创建过程(比如设计单例模式的时候)

  ```python
  class Person:
      def __new__(cls, *args, **kwargs):
          print('创建了一个对象,但是被拦截了')
          
  p = Person()
  print(p) # None 没有分配内存给这个对象
  ```

- `__init__`方法

  创建了一个对象,并且给了一些额外的信息

  ```python
  class Person:
      def __init__(self):
          self.name = 'sd'
  p = Person()
  print(p.name)
  ```

  当对象创建完成后会自动调用这个方法

- `__del__`方法

  对象被释放之后,会自动调用这个方法 和类的描述里面那个`__delete__`方法不一样,注意区分一下, 和切片索引的`__delitem__`也不一样 (好像没学过`__delattr__`的)

- 小案例: 创建一个实例计数加一,删除一个计数减1

  先实现功能: v1

  ```python
  count = 0
  class Person:
      def __init__(self):
          global count
          count += 1
          print('计数加1')
      def __del__(self):
          global count
          count -= 1
          print('计数减1')
      pass
  p = Person()
  print(f'人数{count}')
  def p
  print(f'人数{count}')
  ```

  这样写有点难看

  首先这个`print`是和类相关的,应该封装到类里面

  ```python
  count = 0
  class Person:
      def __init__(self):
          global count
          count += 1
          print('计数加1')
      def __del__(self):
          global count
          count -= 1
          print('计数减1')
      @staticmethod
      def log():
          print(f'人数{count}')
      pass
  p = Person()
  p2 = Person()
  Person.log()
  def p
  Person.log()
  ```

  然后这个全局变量并不安全,且没有把这个变量封装进去,封装成类属性比较好

  ```python
  class Person:
      __count = 0
      def __init__(self):
          self.__class__.__count += 1
          print('计数加1')
      def __del__(self):
          Person.__count -= 1
          print('计数减1')
      @classmethod
      def log(cls):
          print(f'人数{cls.__count}')
      pass
  p = Person()
  p2 = Person()
  Person.log()
  def p
  Person.log()
  ```

### 8.7.2 Python的内存管理机制

- 存储方面:

  - 万物皆对象

  - 所有对象,都会再内存中开辟一块空间进行存储,会根据不同的类型以及内容,开辟不同大小的空间,返回该空间的地址给外界接收(即引用),用于后续对这个对象的操作

    可以通过`id`函数查看地址,是一个十进制的数据,可以用`hex`函数转成十六进制的

  - 对于整数和短小的字符,python会进行缓存,不会创建多个相同的对象

    ```python
    num1 = 2
    num2 = 2
    print(id(num1))
    print(id(num2))
    ```

    是相同的, 这样是为了提升性能 

  - 容器对象,存储的其他对象,仅仅是其他对象的引用,并不是对象的本身

- 垃圾回收机制

  - 引用计数器机制: 一个对象,会记录着自身被引用的个数,每增加一个引用则加1,减少一个引用就减1

    例子:

    ```python
    class Person:
        pass
    p1 = Person() # p1存放地址
    p2 = p1 # 将p1的值赋值给p2,所以p2也是这个对象的地址
    del p2 # 减少一个引用
    del p1 # 再减少一个引用,此时引用个数变成了0
    ```

    查看引用个数 使用 `sys.getrefcount(对象)`,注意这个传递过去之后会自动加1,所以这个返回的值比你想象的要大1

    ```python
    import sys
    class Person:
        pass
    p1 = Person() # p1存放地址
    print(sys.getrefcount(p1)) # 2 (不是1)
    p2 = p1 
    del p2 # 减少一个引用
    del p1
    ```

    **引用计数器加1的场景**

    - 对象被创建的时候: `p1 = Person()`
    - 对象被引用的时候: `p2 = p1`
    - 对象被作为参数,传入到一个函数中的时候
    - 对象作为一个元素,存储在容器中时 : `l = [p1]`

    **引用计数器减1的场景**

    - 对象的别名被显式销毁 `del p1`
    - 对象的别名被赋予新的对象 `p1 = 123`
    - 一个对象离开它的作用域:比如函数执行完毕时,内部的局部变量关联的对象
    - 对象所在的容器被销毁,或者从容器中删除对象

    循环引用问题:

    会造成内存泄漏问题: 比如人和狗类相互引用

    ```python
    class Person:
        pass
    class Dog:
        pass
    p = Person()
    d = Dog()
    p.pet = d
    d.master = p # 循环引用了
    
    ```

    ![image-20211212231801863](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211212231801863.png)

    删除之后

    ```python
    del p
    del d
    ```

    之后是不是引用计数器还有1呢? 但是也没与办法释放了,这样是不是就内存泄露了呢?

    借助一个工具 `objgraph`

    `objgraph.count`可以追踪对象的个数

    ```python
    import objgraph
    print(objgraph.count('Person'))
    print(objgraph.count('Dog'))
    ```

    发现删除之后也是`1 1`,说明确实没有释放掉

    但是python还有一套垃圾回收机制来补充引用计数器机制的不足之处,比如循环引用机制

    python为 **引用计数器机制 + 垃圾回收机制**

  - 垃圾回收机制

    - 主要作用: 从经历过"引用计数器机制"仍未被释放的对象中,找到"循环引用",干掉相关对象

    - 底层机制(有点难)

      怎么找到循环引用?

      1. 收集所有的"容器对象",通过一个双向链表进行引用

         容器对象就是可以引用其他对象的对象比如列表 字典 元组 自己创建的对象

         非容器对象: 比如整形对象 布尔 浮点 字符串对象等 (你都不能引用其他对象怎么可能循环引用呢)

      2. 针对于每个"容器对象",通过一个变量`gc_refs`来记录当前相应的引用计数

      3. 对于每个"容器对象",找到它引用的"容器对象",并将这个"容器对象"的引用计数-1

      4. 经过步骤3之后,如果一个"容器对象"的引用计数为0,就代表这玩意可以被回收了,肯定是"循环引用"导致它活到现在的

         ![image-20211213135024718](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211213135024718.png)

      如何提升查找"循环引用"的性能?

      分代回收机制

      - 默认一个对象创造出来之后属于0代

      - 如果经历一次垃圾回收之后,仍存活下来,那么分到下一代

      - 垃圾回收的周期顺序为: 0代回收一定次数,会触发0代和1代回收, 1代回收一定次数,会触发0代 1代 2代回收

        > 对象的命越大,那么它的寿命越长

      - 查看和设置相关参数

        来及回收期当中新增的对象的个数 - 消亡的对象的个数达到一定的阈值的时候,才会触发垃圾回收机制

        - `import gc`

        - `print(gc.get_threshold())`

        - `gc.set_threshold(700,10,5)` # 阈值 0代检测了10次时触发1代, 1代检测5次触发2代

          > 可以适当增大间隔,因为垃圾检测做一次耗时耗力

    - 垃圾回收触发的时机:

      - 自动回收: 

        触发条件: 

        1. 开启了垃圾回收的机制

        ```python
        gc.enbale() # 默认开启
        gc.disable() 
        gc.isenabled()
        ```

        2. 新增的对象的个数 - 消亡的对象的个数达到了阈值

        ```python
        gc.set_threshold(700,10,5)
        ```

      - 手动回收

        ```python
        import gc
        import objgraph
        gc.disable() # 可以先关闭了这个
        class Person:
            pass
        class Dog:
            pass
        p = Person()
        d = Dog()
        p.pet = d
        d.master = p # 此时有一个循环引用
        del p
        del d
        
        print(objgraph.count('Person'))
        print(objgraph.count('Dog'))
        # 都是1 没有释放掉
        
        # 手动触发一下垃圾回收机制
        gc.collect() # 这个函数有可选参数的,看一下文档,好像是和变量的代数(generation)有关系
        ```

      - 强引用和弱引用: 不会使引用计数加1的引用就是弱引用, 这样就会被释放掉了

        ```python
        import weakref
        import gc
        import objgraph
        gc.disable() # 可以先关闭了这个
        class Person:
            pass
        class Dog:
            pass
        p = Person()
        d = Dog()
        p.pet = d 
        d.master = weakref.ref(p) 
        del p
        del d
        print(objgraph.count('Person'))
        print(objgraph.count('Dog')) # 都是0 代表是被释放掉了
        ```

        这样 `Person`有一个引用. `Dog`有两个引用, 干掉p之后, `Person`也就没有引用了, 对象都没了,所以`Dog`的引用也会减少一个,变成`1`,`del d`之后久变成0引用了,所以就没有引用了, 干掉了已经, 解决了循环引用

      - 还有一种方法, 在`del p`之前先释放掉`p.pet`就好了 `p.pet = None`

      - `weakref`是解决一个对象的弱引用,多个呢? 弱字典可以解决

        ```python
        import weakref
        import gc
        import objgraph
        gc.disable() # 可以先关闭了这个
        class Person:
            pass
        class Dog:
            pass
        p = Person()
        d = Dog()
        
        p.pet =  weakref.WeakValueDictionary({'dog':d1, 'cat': c1})
        
        d.master = weakref.ref(p) 
        del p
        del d
        ```

## 8.8 一个综合案例

### 8.8.1 需求

- 计算器: 实现一些基本的操作, 加减乘除运算, 以及打印结果操作

### 8.8.2 实现(很精彩)

- 如果用函数来实现, 调用起来太复杂了

- 用面向对象的想法来解决这个问题 

  ```python
  # ver 1
  class Calculator:
      result = 0
      @classmethod
      def first_value(cls,v):
          result = v
      @classmethod
      def add(cls,v):
          result += v
      @classmethod
      def sub(cls,v):
          result -= v
      @classmethod
      def mul(cls,v):
          result *= v
  Calculator.first_value = 2
  Calculator.add(6)
  Calculator.sub(4)
  Calculator.mul(5)
  print(Calculator.result)
  ```

  问题: 可以随意修改result, 所以让其变成私有属性会好一点,但是此时没法读取了, 所以使用一个`get`方法来获得`result`的值会好一点

  ```python
  #ver 2
  class Calculator:
      __result = 0
      @classmethod
      def first_value(cls,v):
          __result = v
      @classmethod
      def add(cls,v):
          __result += v
      @classmethod
      def sub(cls,v):
          __result -= v
      @classmethod
      def mul(cls,v):
          __result *= v
      @classmethod
      def show(cls):
          print(__result)
  Calculator.first_value = 2
  Calculator.add(6)
  Calculator.sub(4)
  Calculator.mul(5)
  Calculator.show()
  ```

  问题: 用类属性, 无法复用呀, 没有办法在不同文件中使用,大家改的都是同一个属性的值呀,明智之举是将其改成实例属性,而不是类属性

  ```python
  # ver 3
  class Calculator:
      def __init__(self,num):
          self.__reslut = num
      def add(self,v):
          self.__result += v
      def sub(self,v):
          self.__result -= v
      def mul(self,v):
          self.__result *= v
      def show(self):
          print(self.__result)
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  问题: 没有做容错处理,增加判断整形的方法

  常规做法:

  ```python
  class Calculator:
      def __init__(self,num):
          if not isinstance(num,int):
              raise TypeError('不是整形')
          self.__reslut = num
      def add(self,v):
          if not isinstance(v,int):
              raise TypeError('不是整形')
          self.__result += v
      def sub(self,v):
          if not isinstance(v,int):
              raise TypeError('不是整形')
          self.__result -= v
      def mul(self,v):
          if not isinstance(v,int):
              raise TypeError('不是整形')
          self.__result *= v
      def show(self):
          print(self.__result)
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  但是这个冗余度比较高,封装成一个方法比较好

  ```python
  class Calculator:
      def check_num(self,num):
          if not isinstance(num,int):
              raise TypeError('不是整形')
      def __init__(self,num):
          self.check_num(num)
          self.__reslut = num
      def add(self,v):
          self.check_nun(v)
          self.__result += v
      def sub(self,v):
          self.check_nun(v)
          self.__result -= v
      def mul(self,v):
          self.check_nun(v)
          self.__result *= v
      def show(self):
          print(self.__result)
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  但是这个也不是好呀, 我们希望不改变Ver3的版本, 而且这个代码违背了单一职责的原则呀

  ```python 
  # ver 4
  class Calculator:
      def check_zsq(func):
          def check_num(self,num):
              if not isinstance(num,int):
                  raise TypeError('不是整形')
              res = func(self,num)
              return res
          return check_num
      @check_zsq
      def __init__(self,num):
          self.__reslut = num
          
      @check_zsq
      def add(self,v):
          self.__result += v
          
      @check_zsq
      def sub(self,v):
          self.__result -= v
          
      @check_zsq
      def mul(self,v):
          self.__result *= v
          
      def show(self):
          print(self.__result)
          
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  问题: 这里的装饰器是一个实例方法呀, 不太好吧, 所以需要把这个装饰器变成一个私有的方法

  ```python
  # ver 5
  # ver 4
  class Calculator:
      def __check_zsq(func):
          def check_num(self,num):
              if not isinstance(num,int):
                  raise TypeError('不是整形')
              res = func(self,num)
              return res
          return check_num
      @__check_zsq
      def __init__(self,num):
          self.__reslut = num
          
      @__check_zsq
      def add(self,v):
          self.__result += v
          
      @__check_zsq
      def sub(self,v):
          self.__result -= v
          
      @__check_zsq
      def mul(self,v):
          self.__result *= v
          
      def show(self):
          print(self.__result)
          
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  现在需要增加一个新的功能了, 需要增加一个语音播报的功能

  ```python
  import win32com,client
  # 创建播报器
  speaker = win32com.client,Dispatch('SAPI.SpVoice')
  # 播放
  speaker.Speak('我的名字是sd')
  ```

  还是用装饰器来实现比较好吧

  ```python
  # ver 5
  # ver 4
  import win32com,client as c
  
  class Calculator:
      def __check_zsq(func):
          def check_num(self,num):
              if not isinstance(num,int):
                  raise TypeError('不是整形')
              res = func(self,num)
              return res
          return check_num
     	def __say(func):
          def inner(self,num):
              speaker = c.Dispatch('SAPI.SpVoice')
              speaker.Speak(str(num))
              res = func(self,num)
              return res
         	return inner
      @__check_zsq
      @__say
      def __init__(self,num):
          self.__reslut = num
      
      @__check_zsq
      @__say  
      def add(self,v):
          self.__result += v
      
      @__check_zsq
      @__say  
      def sub(self,v):
          self.__result -= v
         
      @__check_zsq
      @__say 
      def mul(self,v):
          self.__result *= v
          
      def show(self):
          print(self.__result)
          
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  > 这里嵌套为啥是__say在下面? 我们需求是先验证再播放, 所以就应该是验证代码是套在最外层的,所以应该确实是这样写的

  问题: 我希望播放操作符 ,所以需要装饰器可以变一变

  ```python
  # ver 5
  # ver 4
  import win32com,client as c
  
  class Calculator:
      def __check_zsq(func):
          def check_num(self,num):
              if not isinstance(num,int):
                  raise TypeError('不是整形')
              res = func(self,num)
              return res
          return check_num
      
      def __say(op='')
          def __say_zsq(func):
              def inner(self,num):
                  speaker = c.Dispatch('SAPI.SpVoice')
                  speaker.Speak(op+str(num))
                  res = func(self,num)
                  return res
              return inner
         	return __say_zsq
      @__check_zsq
      @__say()
      def __init__(self,num):
          self.__reslut = num
      
      @__check_zsq
      @__say('加')  
      def add(self,v):
          self.__result += v
      
      @__check_zsq
      @__say('减去')  
      def sub(self,v):
          self.__result -= v
         
      @__check_zsq
      @__say('乘以') 
      def mul(self,v):
          self.__result *= v
          
      def show(self):
          print(self.__result)
          
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  如过像换播放方案呢 而且希望结果也说一下

  ```python
  # ver 5
  # ver 4
  import win32com,client as c
  
  class Calculator:
      def __check_zsq(func):
          def check_num(self,num):
              if not isinstance(num,int):
                  raise TypeError('不是整形')
              res = func(self,num)
              return res
          return check_num
      def __bobao(self, word):
           speaker = c.Dispatch('SAPI.SpVoice')
           speaker.Speak(word)
       
      def __say(op='')
          def __say_zsq(func):
              def inner(self,num):
                  self.__bobao(op+str(num))
                  res = func(self,num)
                  return res
              return inner
         	return __say_zsq
      @__check_zsq
      @__say()
      def __init__(self,num):
          self.__reslut = num
      
      @__check_zsq
      @__say('加')  
      def add(self,v):
          self.__result += v
      
      @__check_zsq
      @__say('减去')  
      def sub(self,v):
          self.__result -= v
         
      @__check_zsq
      @__say('乘以') 
      def mul(self,v):
          self.__result *= v
          
      def show(self):
          self.__bobao(f"等于{self.__result}")
          print(self.__result)
          
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  在加一个需求, 外面想要调用result呢? 使用描述器就可以完成这个功能了

  ```python
  # ver 5
  # ver 4
  import win32com,client as c
  
  class Calculator:
      def __check_zsq(func):
          def check_num(self,num):
              if not isinstance(num,int):
                  raise TypeError('不是整形')
              res = func(self,num)
              return res
          return check_num
      def __bobao(self, word):
           speaker = c.Dispatch('SAPI.SpVoice')
           speaker.Speak(word)
       
      def __say(op='')
          def __say_zsq(func):
              def inner(self,num):
                  self.__bobao(op+str(num))
                  res = func(self,num)
                  return res
              return inner
         	return __say_zsq
      @__check_zsq
      @__say()
      def __init__(self,num):
          self.__reslut = num
      
      @__check_zsq
      @__say('加')  
      def add(self,v):
          self.__result += v
      
      @__check_zsq
      @__say('减去')  
      def sub(self,v):
          self.__result -= v
         
      @__check_zsq
      @__say('乘以') 
      def mul(self,v):
          self.__result *= v
          
      def show(self):
          self.__bobao(f"等于{self.__result}")
          print(self.__result)
      @property
      def result(self):
          return self.__result
      
  c1 = Calculator(2)
  c1.add(6)
  c1.sub(4)
  c1.mul(5)
  c1.show()
  ```

  再简化一下调用的过程:

  ```python
  # ver 5
  # ver 4
  import win32com,client as c
  
  class Calculator:
      def __check_zsq(func):
          def check_num(self,num):
              if not isinstance(num,int):
                  raise TypeError('不是整形')
              res = func(self,num)
              return res
          return check_num
      def __bobao(self, word):
           speaker = c.Dispatch('SAPI.SpVoice')
           speaker.Speak(word)
       
      def __say(op='')
          def __say_zsq(func):
              def inner(self,num):
                  self.__bobao(op+str(num))
                  res = func(self,num)
                  return res
              return inner
         	return __say_zsq
      @__check_zsq
      @__say()
      def __init__(self,num):
          self.__reslut = num
      
      @__check_zsq
      @__say('加')  
      def add(self,v):
          self.__result += v
          return self
      
      @__check_zsq
      @__say('减去')  
      def sub(self,v):
          self.__result -= v
          return self
         
      @__check_zsq
      @__say('乘以') 
      def mul(self,v):
          self.__result *= v
          return self
          
      def show(self):
          self.__bobao(f"等于{self.__result}")
          print(self.__result)
          
      @property
      def result(self):
          return self.__result
     	def clear(self):
          self.__result = 0
      
  c1 = Calculator(2)
  c1.add(6).sub(4).mul(5).show().clear().add(3).sub(2).show() # 链式编程的思想
  
  ```

## 8.9 面向对象的三大特性

### 8.9.1 封装

- 将一些属性和方法封装在一个对象中,对外部隐藏细节
- 好处
  - 使用起来更加方便, 因为已经把很多的功能封装好了, 外界相当于一个工具箱
  - 保证数据的安全: 可以设置私有, 可以控制数据只读, 可以拦截数据的写操作
  - 有利于代码的维护 

### 8.9.2 继承

- 概念: 一个类"拥有"另外一个类"资源"的方式之一

  - 拥有不是复制,而是资源的"使用权"
  - 资源:是指非私有的属性和方法

- 目的:方便资源的重用, 将公有的写到父类,将特有的写到子类

- 分类:

  - 单继承: 仅仅继承了一个父类

    ```python
    class Person(Animal):
        pass
    ```

  - 多继承 继承了多个父类

    ```python
    class Ma(Lv, Luozi):
        pass
    ```

- 查看基类:

  ```python
  Ma.__bases__
  Person.__bases__
  ```

- `object`类:

  - 里面有`__getattribute__`方法

- `type`和`object`区别

  `type`是元类, 就是生成类的模板, `object`是 父类, 可以由子类继承资源的使用权, 这个是完全不同的概念

  ![image-20211214130621716](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214130621716.png)

  ​	![image-20211214130733910](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214130733910.png)

  但是他们之间有联系的, `type`是`object`的类模板, `type`也是继承自`object`

- 对资源的影响:

  - 对资源的继承:

    在Python中,继承是指对资源的使用权

    子类可以访问父类的非私有的属性和方法,即受保护的和公有的属性和方法以及内置的方法

    Python的继承是资源的使用权,不是复制一份

    访问是读取.而不是可以修改,比如父类有个`a = 1`, 子类又给他赋值了,那就是新增一个属性,而不是修改了

    ```python
    class B:
        age = 10
    class A(B):
        pass
    A.age = 9
    print(B.age) # 依然是10, 没有改
    print(A.__dict__)
    print(B.__dict__)
    ```

  - 对资源的使用:

    如果子类和父类有相同名字的资源, 那么访问时是什么样的机制呢?

    - 单继承链:

      ![image-20211214141148270](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214141148270.png)

      查找的时候就是依次向上找

      **从下往上的原则**, 访问的时候, 如果A没有,在B里找, 再去C里找

    - 无重叠的多继承链:

      ![image-20211214141239129](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214141239129.png)

      如果一个属性在 C和D里都有,访问哪一个呢

      **单调原则**: 先去左侧找, 再去右侧找, A -> B ->D ->C- > E

    - 有重叠的多继承链:

      ![image-20211214141321849](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214141321849.png)

      **从下到上的原则**: A ->B  -> C- > D

    **Python对于这个场景的逐步演化过程**:

    - Python2.2之前的版本: MRO - 深度优先

      - MRO: Method Resolution Order 方法解析顺序 
      - 深度优先: 
        - 把根节点压入栈中
        - 每次从栈中弹出一个元素,搜索所有在它下一级的元素, 把这些元素压入栈中
        - 重复第二个步骤到结束为止
      - 在Python 2.2之前 有重叠的多继承链(菱形继承)是不对的, 它运行出来 A -> B -> D -> C  违背了"重写可用原则"

    - Python2.2

      有了新式类`object`

      有了一些修改: 对于菱形继承的类型: 重复的按照最后一次为准(中间多余的不寻找)

      > 注意 **不是广度优先算法**, 无重复的多继承是有问题的

      这个算法是有问题的:

      - 

      ![image-20211214191129856](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214191129856.png)

      ​			这个继承关系是有问题的,但是这个算法没办法检测出来这个继			承(因为继承B完全是无意义的

      ​			A -> C -> B -> D -> O 违背了局部优先的原则

    - Python2.3 - Python2.7 : C3算法

      - 两个公式: `L(object) = object, L(子类(父类1, 父类2)) = [子类] + merge(L(父类1),L(父类2), [父类1, 父类2])`
      - `merge`算法: 很复杂 要用的话就去查查看吧

      ![image-20211214191821702](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214191821702.png)

      ![image-20211214192740356](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214192740356.png)

      - C3算法不是拓扑排序算法

    - Python3.X 的算法: C3算法

    - 不想算,可以直接用模块解决这个问题

      ```python 
      import inspect
      inspect.getmro(A)
      ## 或者来
      Dog.__MRO__
      Dog.mro()# 这样也可以的
      ```

  - 对资源的覆盖:

    由于上面的继承关系, 有一种优先级高的类的属性和方法覆盖了低优先级的类的属性和方法的感觉, 讲本质一点,仅仅是由于资源寻找访问的顺序决定的,我们把这个现象称之为资源的覆盖

    只有优先级高的可以覆盖优先级低的

    - `cls` 和 `self`的问题

      调用优先级比较高的资源的时候, 请注意`self`的变化

      ```python
      class D(object):
          pass
      class C(D):
          pass
      class B(D):
          def test(self):
              print(self)
          @classmethod
          def test2(cls):
              print(cls)
          pass
      class A(B,C):
          pass
      A.test2() # 打印的依然是A类, 谁调用就传谁
      a = A()
      a.test() # 打印的是a
      ```

  - 对资源的累加: 

    - 在一个类的基础之上,增加一些额外的资源
    - 子类比父类,多一些自己特有的资源, 或者在被覆盖的方法基础之上,新增一些内容

    ```python 
    class B:
        b = 2
        def __init__(self):
            self.a = 1
    class A(B):
        pass
    p = A()
    p.a # 因为A创建找不到__init__.所以调用的B的__init__,所以相当于给p新增了一个实例属性a
    
    ```

    ```python
    class B:
        b = 2
        def __init__(self):
            self.a = 1
    class A(B):
       	def __init__(self):
            self.c = 3
    p = A()
    p.c # 可以访问
    p.a # 重写了__init__, 这样就没有a了, 所以访问不到了
    ```

    在子类的方法上调用父类的方法

    ```python
    class B:
        b = 2
        def __init__(self):
            self.a = 1
    class A(B):
        def __init__(self):
            # 可以通过类名调用呀
            B.__init__(self)
            self.c = 3
    p = A()
    p.a #
    ```

    当B的名字改之后,不利于维护,另外可能有重复调用的问题, 经典类中只能用这种方式

    更常用的还是`super`方法

    - 本质点就是一个类, 起着代理作用, 沿着MRO链条, 找到下一级节点,去调用对应的方法(不是严格的父类, 多继承的, 按照MRO的顺序)

    - 问题: 沿着谁的MRO链条, 找谁的下级节点, 如何应对类方法, 实例方法以及静态方法的传参呢

    - 语法原理: `super(参数1[,参数2])`

      ```python
      def super(cls, inst):
          mro = inst.__class__.mro()
          return mro[mro.index(cls) + 1]
      ```

      参数一传一个类, 第二个传一个方法(类方法 实例方法 静态方法)或者其他,这样就能取到这个方法的类的MRO, 然后找的是当前类的下一个节点

      先通过第二个参数找MRO链,然后希望找到希望索引的前一个节点(也就是第一个参数)就行了

      - 第一个参数 是查找哪个类的下一个节点
      - 第二个参数是会调用后面的方法, 然后还会查找这个参数的MRO链条

      常用的用法

      ```python
      super(type, obj) -> bound super object
      super(type, type2) -> bound super object
      super() # 根据上下文环境自动填充 
      ```

      案例

      ```python
      class B:
          def __init__(slef):
              self.b = 2
              
          def t1(self):
              print("t1")
          @classmethod
          def t2(cls):
              print('t2')
          @staticmethod
          def t3():
              print('t3')
      class A(B):
          c = 3
          def __init__(self):
              super(A,self).__init__()
              # 可以写成 super(self.__class__, self).__init__() 但是这样不稳定, 不要这么写 这样会出错的,子类传个self过来会变成 super(A的子类.__class__, self).__init__() 下一个节点依然是A 这样就套娃了 死循环调用了
              # 简化 super().__init__()
              self.e = '5'
          @classmethod
          def tt2(cls):
              super(A,A).t2() 
              # 或者super(A,cls).t2()
      ```

      我想找的是B, 是A的下一个节点,所以第一个参数填A, 第二个参数填写A或者A的实例(因为只有这两个的MRO链条里包含了A),我调用的方法调用的是实例方法,所以我传一个实例对象

      使用这种方法的菱形继承可以不重复调用的(因为链条是单股的)

      注意:

      - 不要动态获取类

        ```python
        super(self.__class__, self).__init__() # 不好
        ```

      - 不要`super()`和类名这样混合使用, 这样会很奇怪

### 8.9.3 多态

- 一个类所延伸的多种形态

- 调用时的多种形态

  ```python
  class Animal:
      def jiao(self):
          pass
      pass
  class Dog(Animal):
      def jiao(self):
          print('汪汪汪')
  class Cat(Animal):
      def jiao(self):
          print('喵喵喵')
  def test(obj):
      obj.jiao()
  d = Dog()
  c = Cat()
  test(d) # 汪汪汪
  test(c) # 喵喵喵
  ```

  

- 多态在Python中的体现

  - python使用的是一个动态类型,你给一个变量啥类型, 它就是啥类型
  - 这叫做 `鸭子类型` : 如果一个对象会走 会游泳 会叫那么他就可以当作一个鸭子来处理, 关注点在于行为和属性 而非对象的类型
  - 所以在Python里**没有真正意义上的多态, 也不需要多态** ,在静态类型语言里才会用到这个

### 8.9.4 抽象类和抽象方法

- 抽象出来的概念,不能直接使用

- Python中无法直接支持, 需要借助`abc`模块

  - 设置类的元类为 `abc.ABCMeta`

  - 类的方法装饰器`abc.abstractmethod`

    ```python
    import abc
    class Animal(metaclass=abc.ABCMeta):
        @abc.abstractmethod
        def jiao(self):
            pass
        # 还有抽象类方法 抽象静态方法等
    ```

    抽象方法必须被子类实现才可以, 抽象类也无法造出来

### 8.9.5 综合案例

- 需求:

  ![image-20211214223615933](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211214223615933.png)

- 版本1:

  ```python
  class Dog:
      def __init__(self, name, age=1):
          self.name = name
          self.age = age
      def eat(self):
          print(f'名字是{self.name}, 年龄{self.age}的小狗在吃饭')
      def play(self):
          print(f'名字是{self.name}, 年龄{self.age}的小狗在玩')
      def sleep(self):
          print(f'名字是{self.name}, 年龄{self.age}的小狗在睡觉')
      def watch(self):
          print(f'名字是{self.name}, 年龄{self.age}的小狗在看家')
  ```

  重复的不要好呀

  ```python
  class Dog:
      def __init__(self, name, age=1):
          self.name = name
          self.age = age
      def eat(self):
          print(f'{self}在吃饭')
      def play(self):
          print(f'{self}在玩')
      def sleep(self):
          print(f'{self}在睡觉')
      def watch(self):
          print(f'{self}在看家')
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的小狗'
  ```

  小猫和人的是类似的

  ```python
  class Cat:
      def __init__(self, name, age=1):
          self.name = name
          self.age = age
      def eat(self):
          print(f'{self}在吃饭')
      def play(self):
          print(f'{self}在玩')
      def sleep(self):
          print(f'{self}在睡觉')
      def catch(self):
          print(f'{self}在捉老鼠')
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的小猫'
  class Person:
      def __init__(self, name,pets, age=1):
          self.name = name
          self.age = age
          self.pets = pets
      def eat(self):
          print(f'{self}在吃饭')
      def play(self):
          print(f'{self}在玩')
      def sleep(self):
          print(f'{self}在睡觉')
      def foster(self):
          for pet in self.pets:
              pet.eat()
              pet.play()
              pet.sleep()
      def make_pets_work(self):
          for pet in self.pets:
            	if isinstance(pet,Dog):
                  pet.watch()
              elif isinstance(pet,Cat):
                  pet.catch()
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的人'
  ```

  这种方法不太好, 可以将宠物的工作全部变成work()就好了

  ```python
  class Dog:
      def __init__(self, name, age=1):
          self.name = name
          self.age = age
      def eat(self):
          print(f'{self}在吃饭')
      def play(self):
          print(f'{self}在玩')
      def sleep(self):
          print(f'{self}在睡觉')
      def work(self):
          print(f'{self}在看家')
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的小狗'
  class Cat:
      def __init__(self, name, age=1):
          self.name = name
          self.age = age
      def eat(self):
          print(f'{self}在吃饭')
      def play(self):
          print(f'{self}在玩')
      def sleep(self):
          print(f'{self}在睡觉')
      def work(self):
          print(f'{self}在捉老鼠')
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的小猫'
  class Person:
      def __init__(self, name,pets, age=1):
          self.name = name
          self.age = age
          self.pets = pets
      def eat(self):
          print(f'{self}在吃饭')
      def play(self):
          print(f'{self}在玩')
      def sleep(self):
          print(f'{self}在睡觉')
      def foster(self):
          for pet in self.pets:
              pet.eat()
              pet.play()
              pet.sleep()
      def make_pets_work(self):
          for pet in self.pets:
            	pet.work()
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的人'
  ```

  这样好扩展起来比较简单

- 版本2: 每个类有很大一部分是相同的, 这个时候应该用继承

  ```python
  class Animal:
      def __init__(self,name , age=1):
          self.name = name
          self.age = age
      def eat(self):
          print(f'{self}在吃饭')
      def play(self):
          print(f'{self}在玩')
      def sleep(self):
          print(f'{self}在睡觉')
          
  class Person(Animal):
      def __init__(self,name, pets, age=1):
          super(Person,self).__init__(name,age)
          self.pets = pet
      def foster(self):
          for pet in self.pets:
              pet.eat()
              pet.play()
              pet.sleep()
      def make_pets_work(self):
          for pet in self.pets:
            	pet.work()
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的人'
      
  class Dog(Animal):
      def work(self):
          print(f'{self}在看家')
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的小狗'
      
  class Cat(Animal):
      def work(self):
          print(f'{self}在捉老鼠')
      def __str__(self):
          return f'名字是{self.name}, 年龄{self.age}的小猫'
  ```

## 8.10 面向对象的设计原则

- 面向对象的设计原则: SOLID
- `S` :单一职责原则 (Single Responsibility Principle)
- `O`: Open Closed Principle : 开放关闭原则 对扩展开放, 对修改关闭
- `L`: Liskov Substitution Principle: 里氏替换原则: 使用积累引用的地方必须能使用继承类的对象 (凡是使用基类的地方一定要可以用子类完全替换): 反例: 小鸟有飞 有子类小燕子和鸵鸟, 使用小鸟的地方,我说可以飞, 但是不能用子类鸵鸟替换啊, 鸵鸟不可以飞, 这样就违背了里氏替换原则
- `I`: Interface Segregation Principle:  接口分离原则
- `D`: Dependency Inversion Principle: 依赖倒置原则: 高层抹开不应该直接依赖底层模块

