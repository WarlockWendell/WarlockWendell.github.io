---

title: python基础语法
date: 2020-3-2 7:58:00
tags: [python]
---

这个学期学习数据库原理需要使用python，所以在这里记录一下python的一些基础语法

<!--more-->

# 1. 基本数据类型

## 1.1 数字

- **整数**：`int`，没有进一步的划分了

  ```python
  int(1.1)		#转成int,得到1，其他类型也有类似用法
  ```

- **浮点数**：`float`，也没有进一步的划分了

  >**除法**
  >
  >- `/`：返回浮点数
  >- `//`：返回整数，这个就像是C语言中的除法，取结果的整数部分

  >**type（）**
  >
  >```python
  >type(1) 			#整形int
  >type(1+0.1)			#浮点型float
  >type(1*0.1)			#浮点型float
  >type(2/2)			#整形除以整形得到浮点型
  >type(2//2)			#要想得到整型，需要使用 //
  >```

  > **数制**
  >
  > - `0b`：二进制
  > - `0o`：八进制
  > - `0x`：十六进制
  > - `bin()`：转成二进制数，`bin(0xE)`得到`0b1110`
  > - `oct()`：转成八进制
  > - `hex()`：转成十六进制

- **布尔类型**

  - 真：`True`，非0数
  - 假：`False`，0

  > 在python中，除了0可以表示False，其他的表示空的也可以表示False
  >
  > ```python
  > bool('abc')		#True
  > bool('')		#False
  > bool([1,2,3])	#True
  > bool({})		#False
  > bool(None)		#False
  > ```

- **复数**

  - 平常应用到的比较少

  - `j`：这个可以表示复数

## 1.2 序列

### 1.2.1 字符串str

#### 1.2.1.1 字符串的表示

- 表示：单引号，双引号，三引号都可以表示字符串，字符串里还有引号就可以用双引号，或者使用转义字符`\`

- 三引号的字符串是可以写多行的（写代码一行不要超过79个字符），使用`\`也可以换行

- `print`函数：显示，可以使用转义字符，在idle中并不会显示出转义字符转义之后的格式，只会显示原格式

- 在字符串之间加上一个`r`或者`R`，变为原始字符串

  ```python
  print(r'hello \n world')
  ```

#### 1.2.1.2 转义字符

- 转义字符是一种特殊的字符：无法“看见”的字符；与语言本身有冲突的字符
- `\n`：换行符
- `\\`：`\`字符
- `\'`：单引号
- `\t`：制表符
- `\r`：回车字符

#### 1.2.1.3 字符串的运算

相比C，python的字符串运算实在是太舒服了

- 合并字符串：`+`

- 重复：`*`，内容重复，但是结果是一个字符串，不是三个

- 截取字符：`[]`，下标，从0开始编号，输入负数就是从后面开始选取，注意负数就是从-1开始的，不是-0

- 截取片段：`[m:n]`，截取从m到n-1个字符，左闭右开，可结合前面讲的负数表示，如果要表示最后一位，n就要是最后一位的后一位，但是这是不存在的，但是python可以执行，且写最后一位的后x位都是可以的，即输入的数字大于整体长度，是会按照最大的来取的，并不算错误（C语言这里就会崩掉）

  如果有缺省`[:3]`,就是从0位到3位，`[3:]`就是从3位到最后一位，都缺省就是整个字符串

### 1.2.2 列表list

- 定义：`[1,2,3,4,5,6]`，用`,`隔开元素，最后再加一个逗号也不会报错，最后一个是空的，是不存在的，不能被访问
- 列表里的元素是可以加入任意元素的，也不要求同一列表内的元素类型相同，还可以嵌套列表（二维数组）
- 也还是使用`[]`来取列表中的元素，使用方法大致是一样的，区别在于当使用`[:]`时，得到的是一个列表，而不是其中元素的类型

- `+`：合并列表
- `*`：重复，得到**一个** 元素重复几次的列表

### 1.2.3 元组tuple

- 定义：`(1,2,3,4,5,6)`，当内部只有一个元素时，也要用小括号隔开，否则会有歧义，空的不需要加逗号，一个空括号就好了
- 元组的内部元素也不要求是同一种元素，访问方式和列表也是类似的
- `+` 、`*`：这个和列表也是一样
- 元组不可变，列表可变

### 1.2.4 序列的一些共有特点

- 每个元素都有自己的编号

- 切片：`[m:n:l]`，m是起始，n是终止后一位，l是步长

- `+` `*`

- `in`：是否在这个序列中，返回值是布尔类型，`not`加上之后就是判断是否不在

- `len`：长度统计函数 `len[1,2]`

- `max`：最大值 `max([1,2,3])`；字符串也可以，比较ASCII编码；同理也有最小值

  > 有意思的是，**序列之间也是可以比较大小**的，就和数字比大小一样，一位一位的比，是有顺序的

- `ord()`：显示字符的ASCII码

- 使用`list`、`tuple`应该可实现相互转换，但是不能转换数字，`str`可以转换数字

## 1.3 集合set

- 集合是无序的：由于是无序的，所以没有索引和切片
- 定义：`{1,2,3,4,5}`或者`set('12345')`
- 无重复元素：互异性
- 支持`len`和`in`
- 可以做“减法”：`-`，求两个集合的差
- `&`：做交集
- `|`：做并集，由此可以注意到集合是没有`+`拼接的
- `^`：异或，不公共的元素，取交集，去掉公共的元素
- 空集合：`set()`就是定义一个空集合；不是`{}`，这个是个字典

## 1.4 字典dict

- key value键值对应

  > 有点像C语言的enum

- `{key1:value1,key2:value2...}`
- `{'Q':"新月打击",‘W':"苍白之瀑"}`
- 字典也是无序的
- 访问方式：`[key]`，`{'Q':"新月打击",‘W':"苍白之瀑“}['Q']`就访问了“新月打击”，一个字典中是不能有相同的key值的，相同的会被再次赋值，最后只剩一个
- value：可以使用任意类型，甚至是一个dict；key：必须是不可变的类型，比如int、float、str，但是list是不行的，tuple是不可变的
- 空字典：`{}`



# 2. 运算符

## 2.1 变量

- 定义：直接定义就好，`=`赋值，不用特别指明类型

- 命名规则

- 保留关键字

  ```python
  import keyword
  keyword.kwlist
  ```

- 函数名可以定义成变量，但是这个函数就不能再用了，这就是错误的，所以千万不要用常用函数名来做变量名，如果不慎这么干了，可以用`del`关键字删除变量

- ```python
  a = 1 #a得到一个地址
  b = a #b和a得到相同地址
  a = 3 #a的地址改变，b不改变
  print(b)	#得到b = 1
  
  a = [1,2,3] #a得到一个地址
  b = a	#b得到和a一样的地址
  a[0] = '1'	#a和b的地址都不改变，在原先基础上上变
  print(b)	#得到b = ['1',2,3]
  ```

  为什么会出现这种情况：

  值类型：int、float、complex、bool、str、tuple，因为它们是**不可变**的，想要改变时会生成一个新的，开辟了一个新的地址，原地址的没有被改变

  引用类型：list、dict、set，因为它们是**可变**的，在原来的基础上改变

  > 可使用`print(id(a))`来查看地址，就可以知道修改了什么

- `append`：追加元素，列表可以用，元组不可用，因为元组不可改变

- 注意：元组里的嵌套列表是可以改变的

## 2.2 运算符

- 算术运算符：`**`

- 赋值运算符：python是没有自增、自减运算符的，赋值运算符的优先级很低

- 关系运算符：返回值时bool

- 逻辑运算符：`and` `or` `not`，操作数是bool，返回值也是bool，`not`高于`or`高于`and`

  > 空字符串、列表、元组、集合、字典是False，有值就是True

  > and和or的返回规律
  >
  > 返回最后一个需要使用的操作数
  >
  > ```python
  > 1 and 2 #返回2 因为读到1，还不能确定，再看一个，所以返回2
  > 0 and 1 #返回0 因为读到0就gg了
  > 1 or 2 #返回1，因为读到1就是真了，不用看
  > 0 or 2 #返回2，因为读到0还不能确定，再看一个2，所以返回2
  > ```

- 位运算符：`&` `|` `^` `~` `>>` `<<` 

- 成员运算符：`in` `not in`：在或不在，返回bool

  > 字典成员运算符：
  >
  > 字典的成员运算是查找key的，不是value

- 身份运算符

  `is` `is not` ：返回bool

  针对对象而言的，比较两个变量的身份是否相同（简单理解成内存地址id是否相同）（你的面包不是我的面包）

  对象的三个特征：值、身份、类型

  > `isinstance(对象,类型)`可以查看是否是相应类型，类型可以使用元组表示多种对象
  >
  > 那为什么不用type判断呢？因为它不能判断子类型  



# 3.表达式

- python语句末尾不强制加分号，但是可以加
- python靠缩进区分代码和代码块，因此对代码的格式要求十分严格，python就不可以混淆压缩，所以做python开源性很好
- 可以证明：条件、循环、分支就可以完成程序功能了

## 3.1 条件控制

- `if` `else`

- 后面用`:`表示进入条件控制语句

- 多级嵌套

  ```python
  if condition:
  	pass
  else:
  	if condition:
  		pass
  	else:
  		pass
  ```

- `elif`：这个关键字可简化代码嵌套级别

- `if`可以单独使用

- `pass`是空语句，为了保持代码的完整性，在构造基本结构时经常使用

```python
a = 4
b = 3

if(a>b):
	print("a>b")
else:
	print("a<b")
```

## 3.2 循环

- `while` `for`

- ```python
  while condition:
  	pass
  
  #还可以结合else使用
  while condition:
      pass
  else:
      pass
  ```

- 递归时`while`比较常用

- ```python
  for target_list in expression_list:
      pass
  ```

- `for`循环用来遍历/循环 序列或者集合、字典

- `else`也可以结合使用，遍历完了之后执行这个语句，这个用的应该也不多

- 使用`break`语句跳出当前循环，连后面的`else`语句都不执行了，因为跳出了整个循环语句

- 使用`continue`可以跳过此次循环

- `for(i=0;i<10;i++)`的python写法：`range(起始,终止数字(不包括）,步长)`

  ```python
  for in range(0,10):
  	pass
  ```




# 4. 包、模块

- 包理解为文件夹，模块就理解为文件夹中的文件，在文件中写一个一个的类会比较好，类里面有函数和变量

- 一般不要在模块中写业务代码，不是一个很好的习惯，一般写在类里面

- 命名：包的名字就是文件夹名字，不同包里可以有相同的模块名称`top.module1` ，然后模块的名字就是文件的名字。

  > 包所在的文件夹下要有\__init\_\_.py模块才能成为包，不然不是包，这个文件本身也是一个模块，里面可以不放置内容，只是用来标志这是一个包，这个
  >
  > 命名空间：指定是在哪个包里面的模块。包里面还会有子包。比如包名是seven，里面一个模块是module,可以使用seven.module来表示使用的是这个包。但是_init\_.py是一个特殊的模块，它的名字就是包的名字seven
  
- import导入模块，不能够导入模块中的变量

  ```python
  import module_name
  #使用时要使用 . 
  
  #下面是module1文件的内容
  a = 4
  print(a)
  
  #下面是module2文件中的内容，引入一定要按照命名空间的原则，即路径要明确
  import module1 
  print(module2.a)
  ```

  引入之后会自动生成一个_pycache\_文件夹，里面有一个.pyc文件，这是python生成的自解码文件，基础运用时不需要做过多了解

  > vscode技巧：
  >
  > 在设置中的 files exclude中可以设置隐藏一些不必要的文件

  但是每次引用模块中的变量时都需要写一遍模块名，很麻烦，这里可以使用as关键字解决这个问题

  ```python
  import module1 as m
  print(m.a)
  ```

- from关键字导入模块内变量、函数、模块，这种方式不需要命名空间

  ```python
  #上面的代码可以使用下面的方式实现
  from module1 import a
  print(a)
  #这个方式可以导入函数
  from module_name import var def...
  #使用 from module import * 可以引入全部的变量
  ```

  > 在模块中使用内置属性 _all\_ = ['a','c'] 可以在使用*导入时只导入a和c两个变量，而不是全部的变量

- 如何一次导入多个变量，使用`,`分隔，如果太多怎么换行，在行尾加上一个`\`，但是很难看，可以使用`()`来在变量列表中换行，在导入的变量两边加上括号

  ```python
  from module import(a,b,c,d,
                    e,f,g)
  
  from module import a,b,c,d,\
  					e,f,g
      
  from module import a,b,c,d,e,f,g
  ```

- import一个包的时候，下面的`__init__.py`文件会被python自动执行，不需要手动执行，所以经常在这个文件中做一些初始化的事情。使用from导入变量的时候，也会有这么一个过程；导入一个模块的时候也会有相同的结果

- `__init__.py`文件中的一些注意点

  - `__all__ = ['module1']`可以选择导入保重的哪些模块
  - 批量导入: 如果一系列的模块都需要引用一些相同的库，可以将引用的模块语句(import语句)写在`__init__.py`文件中，这样只需要导入这个文件对应的一个包就好了，但是在使用的时候需要注意命名空间的问题
  
- 包和模块是不会重复导入的，导入了一次之后就不会再导入了

- 不要循环导入，循环的引用是不好的设计方式，尽量不要这样设计，使用其他方式避免这种引用



# 5.函数

- `round()`，可以接受两个参数，第一个是数字，第二个是精确到的位数，缺省第二个表示0

- `help()`，在命令行中查看内置函数的作用

- 定义

  ```python
  def funcname(parameter_list):
  	pass
  ```

  - 参数列表可以没有
  - `return`返回，无就是None
  - 函数名称不要使用python内置的函数名称，否则会出现莫名其妙的情况

- 设置最大递归深度

  ```python
  import sys
  sys.setrecursionlimit(1000000)
  ```

- 如何让函数返回多个结果

  ```python
  def damage(skill1,skill2):
      damage1 = skill1*3
      damage2 = skill2*2
      return damage1,damage2
  damages = damage(3,6)
  ```

  输出的数据damage是元组(tuple)类型，元组可以使用[]这种序号的方式访问，但是这种方式并不是什么好的方式

  可以使用下面的方式，序列解包的方式，这种方式使变量名有意义

  ```python
  damage_skill1,damage_skill2 = damage(3,6)
  ```

- 序列解包

  ```python
  a,b,c = 1,2,3
  #等价于下面的三句话
  a = 1
  b = 2
  c = 3
  
  d = 1,2,3 #d是一个元组
  a,b,c = d #这和上面的例子的结果一样
  a,b,c = [1,2,3] #这样也可以
  a,b,c = (1,2,3) #感觉元组括号似乎可以省？
  ```
  
- 参数

  - 必须参数: 调用的时候需要给它赋值

  - 关键字参数: 调用的时候指定变量值

    ```python
    #必须参数
    def add(x,y):
        return x+y 
    #关键字参数
    add(y=3, x=2) #方便，可读性
    ```

  - 默认参数（缺省值): 必须参数要放在前面，如果顺序不对，使用关键字参数的方式传递

    ```python
    def add(x,y=0,z=0):
        return x+y+z
    
    add(1,2) #x=1,y=2,z=0
    ```

# 6.面向对象

- 类：封装代码

  ```python
  class Student():
      name = ''
      age = 0
      
      def print_file(self):    #这个self是不可以少的，不接受参数也要加这个参数,当然可以叫做其他名字，并不一定是self
          print('name:' +self.name)
          print('age:' + str(self.age))
       
  ```

- 实例化

  ```python
  student = Student()
  student.print_file()
  ```

- 做实际的项目的时候，将类的使用放在其他的文件模块中，定义和实例化在不同的模块中（类也可以使用from引入)

- 设计面向对象时，要学会找行为和执行行为的主体，以及类的特征，类就像是一个模板，通过这个模板可以产生许多个对象

- 构造函数

  ```python
  class Student():
      s_sum = 0
      
      def __init__(self,name,age):	#自动运行，但是也可以使用student._init_()调用,只可返回None
          self.name = name
          self.age = age
      def do_homework(self):
          print("homework")
          
  student1 = Student("张三",18)
  print(student.name) #实例变量
  print(Student.name)	#类变量
  ```

  - 构造函数可以用来生成不同的对象

- 类变量和实例变量

  - 类变量和类相关，实例变量和对象相关，上面的例子中name和age是类变量，self.name和self.age是实例变量
  - 访问的实例变量不存在时，就去类变量中查找

- `__dict__`变量，对象下的所有变量字典，可以做验证使用

  ```python
  print(student1.__dict__)
  ```

  类的`_dict_`变量里有很多变量，也可以使用上诉方法查看

- `self`

  - 实例方法中参数列表中要放入`self`，调用时不需要传入这个变量
  - `self`是指当前调用的某个方法的对象，代表的是实例，而不是类
  - `self`是不能隐藏的，C++中的`this`就不需要指明，需要区分

- 如何在实例方法中访问类变量，也可操作类变量

  ```python
  class Student():
      s_sum = 0
      
      def __init__(self,name,age):
          self.name = name
          self.age = age
          print(Student.s_sum)#访问类变量方法1
          print(self._class_.s_sum) #方法2
          self.__class__.s_sum += 1
      def do_homework(self):
          print("homework")
  ```

- 类方法

  ```python
  @classmethod
  def plus_sum(cls): #cls就像那个self，也不是唯一的，可以改名字，实例方法和类方法判断是上面是否有@classmethod
      cls.s_sum += 1
      
  Student.plus_sum()
  student1 = Student("zhangsan",19)
  student1.plus_sum() #对象调用也是可以的，只不过这在逻辑上说不通
  ```

- 静态方法: 可以使用类方法代替，建议不要经常使用，感觉和类联系不大

  ```python
  @staticmethod
  def add(x,y):
      print(x+y)
  ```

  - 不需要传入`self`这种参数

  > Q:类方法和静态方法是否可以访问实例变量?
  >
  > A:不可以，实例需要实例化才有的，实例变量不一定有的

- 公开和私有成员(变量和方法) 

  `public`  `private`

  上面提到的都是不安全的，外部不应该可以随意访问内部变量

  使用类里面的方法来修改对象内的变量

  默认是`public`的

  ```python
  class Student():
      s_sum = 0
      
      def __init__(self,name,age,score):
          self.name = name
          self.age = age
         	self.__score = 0
      def do_homework(self):
          print("homework")
      @classmethod
      def plus_sum(cls):
          cls.plus_sum += 1
      
      def marking(self,score):
          if score<0:
              return '不要打负分'
          else:
          	self.score = score
  ```

  在变量前面加`__`表示是私有的，但是前后都有的话就不是了，比如构造函数

  但是再调用时会发现还是可以在外部调用，因为因为找不到又自动声明了一个同名变量

  ```python
  student1 = Student('zhangsan',19,34)
  student1.__score = 23 #声明了一个变量
  print(student.__socre) #访问的是内部的变量
  ```

  保护的机制很简单，就是改个名字

  上面的私有变量会被自动更名，然后可以访问

  ```python
  print(student1._Student__score)
  ```

- 继承性

  ```python
  class People():
      sum = 0
      def __init__(self,name,age):
          self.name = name
          self,age = age
  ```

  ```python
  from file import People
  class Student(People):
      def __init__(self,school,name,age):
          self.school = school
          People.__init__(self,name,age)#这里需要传入self
  ```

  为什么需要传入self?

  实例化时调用构造函数是自动的，但是上面代码中就是一个普通的调用，但是这里是一个类调用，不是实例调用，所以需要使用self

  ```python
  from file import People
  class Student(People):
      def __init__(self,school,name,age):
          self.school = school
          super(Student,self).__init__(name,age) #super()找父类
  ```

  同名方法优先调用子类方法

# 7. 小甲鱼

## 7.1 random

- random 
- `random.randint(1,10)` : 生成1到10之间的随机数

## 7.2 Python的一些数值类型

- 整形

- 浮点型

- python的数据类型是不受多少位的限制的 ，硬要说，限制于内存大小

- e记法： 1e10 1e-10  这种记号**统统是浮点型**

- bool: 特殊的整形

  ```python
  True + True # = 2
  True / False # 分母是0
  ```

- **转`int`不是向下取整，是截断处理，这样效率高**

  ```python
  int(1.2) # 1
  int(-1.2) # -1
  ```

  加0.5就四舍五入了

- `str`转换浮点数的时候还是很神奇的

  ![image-20211120222008839](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211120222008839.png)

- **推荐`isinstance`来判断变量类型**

  ```python
  isinstance(4,int) # True
  isinstance(4,str) # False
  ```

- `3 < 4 < 5` 这种表达式在python是行得通的


## 7.3 三元操作符 条件表达式

- 和C语言的顺序有区别

  ```python
  small = x if x<y else y
  ```

  ```c
  small = x<y ? x: y;
  ```

## 7.4 assert断言

- 当这个关键字后面的条件为假时，程序自动崩溃并抛出`AssertionError`的异常

  ```python
  assert 3 > 4
  ```

##  7.5 for循环

- 不像C语言，python在循环体内修改循环变量，开启新一轮循环时还是上次循环的后继的

  ```python
  for i in range(4):
      i += 1
      print(i)
  ```

  输出是`1 2 3 4`,而不是`1 3`

## 7.6 列表: 打了激素的列表

- **列表里的元素类型可以不一致**

- 方法

  - `append()`：加一个进来
  - `len()`
  - `extend()`: 可以同时加多个进入来，将两个列表合并是吧
  - `insert()`：第一个是`index`，第二个是`element`
  - `number[0]` : 访问元素的方式 `number[-1]`
  - 删除元素:
    - `remove()`: 元素，只会删除第一个，不存在则报错
    - `del`: `del number[0]`这种方式可以
    - `pop()`：栈方法，但是还可以加上索引值比如`pop(1)`,pop出`number[1]`
  - 切片

- 操作符

  - 比较大小：按照字典序比较的方法比较，字符串的比较
  - 拼接 `+`，和`extend()`有点像
  - 重复 `*`
  - 成员关系操作符 : `in` `not in`

- 列出方法`dir(list)`

- `count`: 计算参数出现的次数

- `index`: `index(123)`:找123第一次出现的index,`index(123,4,7)`4到7（不包括7）之间第一次出现123的index，找不到会出`ValueError`

- `reverse`: 反转列表

- `sort`：默认从小到大排序，应该可以加入排序的函数吧

  - `sort(func,key)`:排序函数和key
  - `sort([func,key],rreverse=True)`:反转一下

- 拷贝：

  - `list11 = [1,3,2,99,7,8]`

  - `list12 = list11[:]`

  - `list13 = list11`

    ![image-20211122105331266](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211122105331266.png)

## 7.7 元组 戴上了枷锁的列表

和列表的不同之处：**元组是不可变的类型，一旦定义了就不能改变元素了**

- 修改元组的元素会抛出`TypeError`

- 元组的本体应该是逗号啦

  ```python
  a = 1,2,3 # a是元组
  a = (1) # a不是元组
  a = (1,) # a是元组
  a = () # a是空元组
  a = 1, # a是元组
  ```

- 由于元组是不可变的，但是可以使用拷贝的方法新建一个元组来实现增删改查

  ```python
  temp = (1,2,3,4)
  temp = temp[:2] + (1,)+temp[2:]
  ```

  > 注意上面的方法并不是更改了第一行定义的temp,而是通过切片取出了temp，然后进行拼接赋值给一个新的temp,第一行的那个也还是在的，不过没有标签指向它了，所以会被回收

- 操作符:

  - 拼接: `+`
  - 重复: `*`
  - 逻辑: `>`
  - 成员关系: `in not in`

- `dir(tuple)`

  - `count`
  - `index`

## 7.9 字符串

- 切片：和元组、列表的切片操作是类似的

- 字符串和元组一样**不可以变**

- 但是字符串的方法**十分多**，因为使用频率高

  - `capitalize()`: 第一个字符大写，仅仅第一个，有句号也是第一个，这个有啥用？

  - `casefold()`: 小写

  - `center(width)`: 居中，长度到达width，不足用' '补全，长了就不变，不会截取

  - `count(sub,[,start[,end]])`: 返回sub在字符串中鸿出现的次数，后两个参数表示字符串索引范围

  - `encode(encoding='utf-8',errors='strict')`: 字符串的编码

  - `endswith(sub,[,start[,end]])`: 检查字符串是否以sub结尾，后两个参数表示字符串索引范围，就是检查这个子串是否以这个sub结尾

  - `expandtabs([tabsize=8])`:将tab(\t)转换为空格，默认为8个

  - `find(sub,[,start[,end]])`：没有返回-1，不像index会报错，返回的是第一个出现的

  - `index(sub,[,start[,end]])`:没有会报`ValueError`异常，其它和find一样

  - `format()`

  - `format_map()`

  - `isalnum`:至少有一个字符并且都是字母或者数字为True

  - `isalpha()`:至少有一个字符并且都是字母为True

  - `isascii()`

  - `isdecimal()`:至少有一个字符且只包含十进制数字返回True

  - `isdigit()`:

  - `isnumeric()`

    > 区别
    >
    > ![image-20211122112937841](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211122112937841.png)
    >
    > `isnumeric`可以识别中文的，甚至于罗马等，但是不可以识别`one`
    >
    > ![image-20211122113253886](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211122113253886.png)
    >
    > 震惊

  - `isidentifier()`

  - `islower()`:小写

  - `issprintable()`

  - `isspace()`: 空格

  - `istitle()`：单词的第一个字母是大写，其他的是小写

  - `issupper()`:大写

  - `join(sub)`:以字符串为分割符，插入到sub中所有的字符之间 

    ![image-20211122113959685](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211122113959685.png)

  - `ljust(width)`：左对齐，就像那个center

  - `lower()`:大写转到小写

  - `lstrip()`:去掉左边空格

  - `rstrip()`:去掉右边的空格

  - `maketrans()`:

  - `partition(sub)`: 找到字符串sub，吧字符串分成一个三元组(之前,sub,之后)，如果不包含就是（str1,'','')

  - `removeprefix()`

  - `removesuffix()`

  - `replace(old,new[,count])`:把字符串中old字符替换乘new字符串，count表示不超过count次

  - `rfind(sub[,start[,end]])`:从右边的find

  - `rindex(sub[,start[,end]])`:从右边index

  - `rpartition(sub)`

  - `rjust()`

  - `rsplit()`

  - `split(sep=None,maxsplit=-1)`: 不带参数以空格为分割符切片，maxsplit表示仅风格maxsplit个子字符串，返回一个列表，不包括分割符呀

  - `splitlines([keepends])`：按照'\n'分割，返回一个包含各行的列表，keepends表示返回前keepends行

  - `startswith([refix[,start[,end]]])`:和`endswith`类似

  - `strip([chars])`:删除左右的空格

  - `swapcase()`:大写变小写，小写变大写

  - `title()`: 单词变成大写开头

  - `translate(table)`:根据table的规则（可以由`str.maketrans('a','b')`制定）转换字符串中的字符

    ![image-20211122115534965](https://gitee.com/warlock-wendell/image-bed/raw/master/blog/image-20211122115534965.png)

  - `upper()`: 转换字符串中的所有小写为大写

  - `zfill(width)`,返回长度为width的字符串，源字符串右对齐，前边用'0'填充

- 字符串的格式化

  - `"{0} love {1}.{2}".format('I','python','com')`

  - `'{a} love {b}.{c}'.format(a='I',b='python',c='com')`

  - `'{0} love {b}.{c}'.format('I',b='python',c='com')`

    > 位置参数要在关键字参数前面，比如上面这个，不可以把b或者用0

  - `{{0}}`这样就不是格式化了，就像转义字符

  - `'{0:.1f}'.format(23.234)`: 冒号表示格式化输出的开始

    - `%c`: `'%c'%97` 多个用元组括起来
    - `%s`
    - `%d`
    - `%o`
    - `%x`
    - `%X`
    - `%f`
    - `%e `  `%E`
    - `%g` `%G`: 根据大小智能选择是浮点数还是科学计数法 

# 8. 写代码时遇到的一些问题

## 8.1 for循环变量的作用域问题

- ```python
  for i in [1,2,4]:
      pass
  ```

  执行之后`i`还是有意义的，为4 

- ```python 
  for i in []:
      pass
  ```

  for循环不执行，此时`i`没有定义

- ```python 
  i = 1
  for i in [1,2,4]:
      pass
  ```

  此时全局变量被覆盖，执行完`i`为4

  > 感觉循环变量相当于是新给了一个全局的`i`

- ```python
  a = [num*2 for num in range(10)]
  ```

  这样是不保留`num`的

## 8.2 python变量的作用域问题

- 全局变量在全局都可以被访问，但是未必可以被修改

  ```python
  g = 1
  def a():
      print(g)
      g = 2
  a()
  ```

  此时 访问的不是全局变量g，而是新建的那个g，但是此时还没有被新建，所以不行，这里出错的是`print`这个函数，无法引用到还没有被赋值的`g`，出错的不是因为`g`的冲突

- ```python
  g = 1
  def a():
      global g
      print(g)
      g = 2
  a()
  ```

  这样显式声明一下可以修改全局变量的

- ```python
  g = 1
  def a():
      g = 2
  a()
  ```

  这样是可以的，此时全局变量没有被修改，函数内部新建了一个`g`

  > 函数内部都是新建一个变量，不是赋值一个全局变量 

