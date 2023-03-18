---
title: C++基础语法
date: 2020-5-18 22:22:22
tags: [C++]
---

学习Cpp也有一段时间了，但是还是不是很清晰一些基础的东西，所以记录一下我易忘的知识点

<!--more-->

# 1. Overview

- **operator is function**

  ```c++
  std::cout<<"Hello"<<endl;
  
  //结果例子，其实是把<<重载了，但是这里好像也不是这么个函数，本地我没有运行成功，这就是个例子而已
  std::cout.print("Hello");
  std::cout.print(std::endl);
  ```

- .cpp才会被编译，头文件是不会被编译的，其被使用的方式是通过include Copy之后才会起作用的

- VS中：Ctrl + F7编译单个文件

- How C++ Works?

  编写一个文件，然后编译这个文件，再运行。

  如果是多个文件，比如一个文件中引用另一个文件中的函数时。在这个 文件中要先给一个声明，告诉Compiler这里有一个函数长这个样子，然后linker会去将各个函数的声明和定义联系起来。如果只有声明没有定义，就会有Linking Error

- How Compiler Works?

  将source file 变成 Object file

  预处理 ->  标记解释、解析->机器码

  Cpp并不依赖于文件，文件对于Cpp也没有什么特别的意义，.cpp也只是约定俗成，你只需要告诉编译器按照C++的方式编译，可以编译其他后缀的文件，只要规则对。

  Vs中预处理之后会得到.i文件，是预处理之后的文件，可以在项目属性中选择是否输出预处理之后的文件，但是就不编译产生.obj了

  在编译时选择Debug模式，不会进行优化，运行起来也是比较慢的，但是方便Debug在release模式，会进行大量的优化，去除无用的代码，可以提升运行的速度，但是不利于Debug。

- How  Linker Works?

  将每个独立单元的.obj文件link起来

  程序需要一个入口点，这个一般是main()，但是也可以在VS中修改项目属性，在链接器一栏中可以修改程序的入口点，这个了解一下即可。

  一些常见的问题：

  - 如果在同一个文件中定义了一个函数两次（Cpp的函数相同指的是函数名，返回类型，参数表都相同），那么会得到一个Compiler Error，因为不能生成.obj文件。当在不同文件定义了一个函数两次，那么会得到一个Link Error，因为linker不知道从哪一个.obj中链接对应的函数。

  - 上面提到的一个典型的例子就是，当你在头文件中写函数定义时，再在两个文件中inlcude了，那么这两个函数都会有这么一个函数定义，会得到linking error。解决的方式有

    - 将函数加上 static关键字，表示这个函数只在当前文件中期作用，对其他.obj不可见，这样就不会有重复定义的问题了
    - 加上inline关键字，此时相当于将函数内容直接写到源程序中，这种相当于没有调用函数，当然也不会有linking error
    - 将函数定义写到一个文件中，头文件中只放声明，这样include时只会复制声明，告诉编译器当前编译单元（文件）有这么一个函数，长这个样子，而不会去定义它

  - 当在一个编译单元中声明了一个函数而没有给出定义，编译这个单个文件是能通过的，生成整个项目时就会有linking error，因为没有给出函数定义。但是如果这个编译单元中没有使用这个函数，仅仅给出了声明，那么编译也是能通过的，因为没有linking这个函数的过程。如果是程序中的一个非入口函数调用了另外一个只有声明的函数呢？这样编译不可以通过，因为当前文件中虽然没有调用这个函数，不会有linking的问题，但是其他文件中就不一定不会调用它，所以还是有linking error（可以理解为只要显式地调用了这个函数，不管是在哪里，那就会有error，而如果只给声明没有显式调用，就可以），那么如何解决呢?只需要给函数加上static关键字，那么说明此函数只在本文件中使用，而本文件中没有使用，那么这个函数里面调用其他函数并不会有问题。

    ```c++
    //原始版本，会报错，因为Log找不到（Log在其他文件中也没有定义)
    void Log(const char* msg);
    int Multi(int a,int b)
    {
        Log("Multiply");
        return a * b;
    }
    
    int main()
    {
        Multi(4,5);
        return 0;
    }
    //修改版本，不报错，以内Log没有使用
    void Log(const char* msg);
    int Multi(int a,int b)
    {
        //Log("Multiply");
        return a * b;
    }
    
    int main()
    {
        Multi(4,5);
        return 0;
    }
    
    //会报错，虽然本文件没有使用Multi，更不会使用Log，但是Multi中显式出现了Log，其他文件嗲用Multi时会出问题
    void Log(const char* msg);
    int Multi(int a,int b)
    {
        Log("Multiply");
        return a * b;
    }
    
    int main()
    {
        //Multi(4,5);
        return 0;
    }
    
    //加上static后，只在当前文件中Multi有效，当前文件不调用Multi，所以不调用Log，所以没问题。
    void Log(const char* msg);
    static int Multi(int a,int b)
    {
        Log("Multiply");
        return a * b;
    }
    
    int main()
    {
        //Multi(4,5);
        return 0;
    }
    ```

    当外部需要引用其他文件中的非静态变量，而且不想重新定义的话，可以使用extern关键字，这是一个声明，而不带extern的是定义，定义只能有一个

# 2.  Previous

## 2.1 data

- 整形

  ```c++
  int;//4
  short;//2
  char;//1
  long;//4
  long long;//4
  unsigned;//无符号数
  ```

- 浮点型

  ```c++
  float;//2
  double;//4
  
  //默认是double，即使声明成float也是一个double
  float a = 3.4f; //加上f/F表示float，否则是double
  ```

- bool

  ```c++
  bool a = true;
  bool b = false;
  //占用一个字节，不是一位，因为最小处理单元是一个字节，而且其实质也是一个数据，而不是单词
  ```

- sizeof

  上面的数字表示字节大小，但是这取决于编译器，可以使用sizeof来查看，sizeof可以加括号，也可以不加括号。

## 2.2 function

这个和C中的函数基本是一样的，需要返回类型、名称、参数表

## 2.3 Header File

- `#pragma once` 表示这个文件只会在同一个文件中被include一次，并不代表在整个demo中只能include一次

  ```c++
  //head.h
  #pragma once
  struct playe{};
  //这样如果被include两次了，就会发生这个结构体的重定义，会有Compiler Error
  ```

  > todo: 为什么不同文件中包含同一个struct的定义不算重定义呢？

- `<>`和`""`，后者表示一个相对路径，前者用于标准库

- `ifndef` `define` `endif`预处理指令也可防止重定义

## 2.4 Debug

VS在调试时，可以使用 调试->窗口->内存，查看当前内存的内容，方便调制以及理解程序运行的过程。

在做这个时，可以发现，在Debug模式下，为了方便调试，编译器做了很多的优化，将栈的初始值都初始化为CC，然后在一些必要的地方加上提示，这些在release模式下并没有，所以debug模式下确实是要慢很多。

在调试模式下，还可以转到反汇编编码，这个对理解底层很有帮助。

## 2.5 New a Project

VS项目资源管理器中的一些类似文件夹的并不是文件夹，而是一个过滤器，是方便阅读和管理的，实际上并没有这个文件夹。

在项目属性中可以调整VS的输出目录和中间目录，这个很有用，可以方便管理文件



# 3. syntax

## 3.1 if else 

条件控制语句

## 3.2 loop

for

while

do while

break

continue

return

## 3.3 Pointer

**pointer is address, it's just a integer**，它就是一个地址，然后通过类型名表示这个指针里存放的地址的内容所占用的空间，指针占用的size在同一个机器里是一样的，比如32位机器就是4Byte，为什么要指定它的类型呢？因为这样给这个地址中内容存放一个内容时，会根据指针的类型来指定大小，这时候就容易出现问题了

```c++
char* s = new char[8];
delete[] s;
//其实也就是底层调用了malloc和free，但是这里还是调用构造函数和析构函数，也是一个特性吧
```



## 3.4 Reference

Refernence不会新构建一个对象，而是相当于取了一个别名，reference一定要初始化，它一定是参考一个**已经存在**的变量，本身不占空间

```c++
int a = 9;
int& ref = a;
//相当于a和ref是一样的变量，a是大名，ref是小名

int b = 9;
ref = b;
//这句话不是将ref的参考关系改成b，ref一旦指定了对象就不能更改了，这句话相当于将a = 9;

//int& 这就是相当于一个类型，ref是它的名字，所以使用的时候直接用ref就好了
```

看起来reference比pointer更加简便，为什么还要用指针？

因为指针的功能更强大，比如指针可以更改指向

```c++
int a = 8;
int b = 9;
int* ptr = &a;
*ptr += 2;
ptr = &b;
(*ptr)++;
//上面这段代码就是分别对a和b进行操作
```

## 3.5 Class

- default ： private

- 封装

- struct 和 class

  > struct和class其实没有太大的区别
  >
  > 唯一的区别是，class默认是私有的，struct默认是公有的，struct里也可以加上private关键字，里面也可以放方法（函数）
  >
  > c++保留struct仅仅是为了兼容C的语法，因为C没有class，只有struct

- static  for classes and structs in cpp

  static的变量在这个类的所有实例中是共享的，也就是改变其中一个实例的这个变量的内容，所有实例中的这个变量的内容就会发生改变

  static的函数（方法）可以被直接调用without instance，不依赖于实例

  同样static的变量也不依赖于实例

  ```c++
  #include<iostream>
  
  class Entity
  {
  public:
  	static int x, y;
  	void Print()
  	{
  		std::cout << x << "," << y << std::endl;
  	}
  };
  
  int Entity::x;
  int Entity::y;
  
  int main()
  {
  	Entity e;
  	e.x = 2;   //相当于Enitiy::x  = 2; 下面的都是同理的
  	e.y = 3;
  	Entity e1;
  	e1.x = 4;
  	e1.y = 5;
  
  
  	e.Print();
  	e1.Print();
  
  
  	std::cin.get();
  }
  ```

  > 注意：静态方法不能使用静态的变量。因为非静态的变量是依赖于实例的，而静态的方法不依赖于实例，所以不能正确的运行。

## 3.6 enum

- enum可以指定类型的

  ```c++
  enum Level:short
  {
      Error = 0,Warning,Info
  };
  ```

- 在类里的enum属于这个类的命名空间

  ```c++
  class Log
  {
  	enum Level : short{Error,Waring,Info};
      ...
  }
  
  int main()
  {
      Level A = Log::Error;
  }
  ```

## 3.7 构造函数

一个特殊的方法，每次例化一个类都要运行一次，自动运行的

即使没有定义构造函数，还是有一个default的构造函数的，虽然它并不干活

如果不希望构造实例怎么办？

- 将构造函数隐藏在private中

  ```c++
  class A
  {
      private:
      	A(){}
     	public:
      	void Print(){}
  };
  ```

  

- 构造函数  =  delete；也可以

  ```c++
  class A
  {
      public:
      	A() = delete;
      	void Print(){};
  };
  ```

c++有默认的六个构造和析构函数

- 缺省构造函数 A()
- 析构函数 ~A()
- 拷贝构造函数 A(const A& ref)
- 拷贝赋值算子 A& operator = (const A & ref)
- 移动构造函数 A(const A && ref)
- 移动赋值算子 A & operator=(A && ref)

使用 = delete删除，使用 = default 保持默认

## 3.8 析构函数

在对象被销毁的时候，自动执行析构函数

用来释放内存

在堆上手工分配的内存要释放

如果手动调用析构函数，在被销毁时也还是会被调用一次，这样容易出错



## 3.9 继承

派生类继承了基类的所有元素

## 3.10 虚函数

虚函数允许覆盖基类中的方法

```c++
#include<iostream>

class Entity
{
public:
	std::string GetName() { return "Entity"; }
};

class Player : public Entity
{
private:
	std::string m_Name;
public:
	Player(const std::string& name) :m_Name(name) {}
	std::string GetName() { return m_Name; }
};


int main()
{
	Entity* e = new Entity();
	std::cout << e->GetName() << std::endl;
	Player* player = new Player("Bob");
	std::cout << player->GetName() << std::endl;
	Entity* p = player;
	std::cout << p->GetName() << std::endl;
	std::cin.get();
}
```

在这种情况下，输出的是

```bash
Entity
Bob
Entity
```

将道理我们第三次传入的是player，也就是一个Player型的，应该输出的是playe.Name，也就是“Bob"，然而并没有

因为传入的是一个Entity*，所以调用的是Entity中的方法，而不是派生类中的方法

此时就需要引入虚函数，允许覆盖基类中的方法

```c++
#include<iostream>

class Entity
{
public:
	virtual std::string GetName() { return "Entity"; } //加上virtual
};

class Player : public Entity
{
private:
	std::string m_Name;
public:
	Player(const std::string& name) :m_Name(name) {}
	std::string GetName() override{ return m_Name; } //override，不是必要的，起标识作用，但是位置不要错
};


int main()
{
	Entity* e = new Entity();
	std::cout << e->GetName() << std::endl;
	Player* player = new Player("Bob");
	std::cout << player->GetName() << std::endl;
	Entity* p = player;
	std::cout << p->GetName() << std::endl;
	std::cin.get();
}
```

这里仅仅是加入了一个virtual，就让函数正确运行了

```bash
Entity
Bob
Bob
```

其实，加上virtual后，这时编译器会为它建一张Vtable，这个会牺牲时间空间效率，但是可以正确的干活

## 3.11 纯虚函数

就像是一个接口

仅有接口，没有实现

所以也不能将这个类实例化

只能在派生类中将这些接口实现后才能实例化

```c++
#include<iostream>

class Entity
{
public:
	virtual std::string GetName() = 0;
};

class Player : public Entity
{
private:
	std::string m_Name;
public:
	Player(const std::string& name) :m_Name(name) {}
	std::string GetName() override { return m_Name; }
};

int main()
{
	//Entity* e = new Entity();
	//std::cout << e->GetName() << std::endl;
	Player* player = new Player("Bob");
	std::cout << player->GetName() << std::endl;
	Entity* p = player;
	std::cout << p->GetName() << std::endl;
	std::cin.get();
}
```

上面的代码中，如果Player Class中没有实现GetName()，那么这个类也不能实例化

## 3.12 可见性

- private: 仅供这个类使用，派生类和其他地方都不可以使用，当然，friend可以使用、

  这个一般是只能在内部修改的，外部的类都不可以修改这个

- protected:：供这个类和它的派生类使用，类之外不可以使用

- public: 任何地方都可以使用

  这个供使用者使用

## 3.13 Arrays in C++

```c++
int a[5];
int *b = new int[5];
int* b = new int[5]{1,2}; //带初始化
delete[] b;
std::array<int,5> c; 
```

使用下面的表达式可以获得数组的长度，使用指针形式定义的貌似不可以使用这种方法

```c++
sizeof(array) / sizeof(ElemType) 
```

对于字符型的指针，使用std::cout输出时会自动查找下一个字符，最后输出的是一个字符串，要想输出它的地址。可以强制将这个指针转换成（void\*)（Int*)等类型，这样可以输出地址。使用\*s的形式输出单个字符。

有关std::array

```c++
size(); 
max_size(); //长度
data(); //返回C style的数组
at(); //安全的[]接口，越界时会报错
enpty(); //检测N值是否是0，当std::array<int,0>才是true，没有赋值但是分配了空间也是false
```

需要明确的是，数组的大小是静态的，不能修改

## 3.15 Strings in C++

需要注意的是，字面值常量是无法直接相加的

```c++
std::string s = "Hello" + "World"; //错误
//要保证加号两侧至少一个string对象
std::string s = std::string("Hello") + "World";

//也可以这么用
using namespace std::string_literals;
std::string s = "Hello"s + "World"; // s将一个const char * 转换成了string object
```

```c++
char* s = "Hello";
s[2] = 'a';
//此时修改的是s的拷贝，而非s本身，当然新标准已经不允许这么定义字符串了
```

## 3.16 Const in C++

- 定义一个不希望被修改的变量

  ```c++
  const int MAX = 90;
  int *a = new int;
  a = (int*)&MAX;
  ```

- 指针

  上面的代码中，a既可以改变内容，也可以改变指向

  加上const

  ```c++
  const int* a = new int;
  int const* a = new int;
  *a = 2; //错误
  ```

  不能修改a指向地址的内容了

  ```c++
  int* const a = new int;
  a = &b; //错误
  ```

  不能修改a的指向

  ```c++
  const int* const a = new int;
  ```

- class中的const

  ```c++
  class A
  {
      int m_x, m_y;
  public:
  	int GetX() const
      {
          return m_x;
      }
  };
  ```

  在方法上加上const表示这个方法不允许修改类的成员

  为什么要这么用？

  因为在有些时候，我们传一个对象给函数时，不希望修改里面的内容，所以使用引用+const

  ```c++
  void fun(const Entity& e)
  {
      std::cout<<e.GetX()<<std::endl;
  }
  ```

  这个时候要保证函数里调用的方法不会修改成员，所以GetX()需要限定成const类型的

  如果还是希望有一些变量可以修改怎么办？

  ```c++
  class A
  {
      int m_x, m_y;
      mutable int c;
  public:
  	int GetX() const
      {
          c = 2;
          return m_x;
      }
  };
  ```

  使用mutable可以使变量在const方法中被修改

## 3.17 Mutable in C++

- 一种是上面提到的和const一起使用

- 在lambda表达式中使用

## 3.18  构造函数初始化列表

这个可以参照另一篇文章，我的另外一篇文章有详细笔记

## 3.19 在C++中创造对象

主要是分为在堆上和在栈上

```c++
void fun()
{
    Entity* e;
    {
        Entity entity;
        e = &entity;
    }
}
```

在里层括号跳出后，就会发生错误，因为e的引用是分配在栈上的跳出后就被pop了

使用在堆上分配的方式可以

```c++
void fun()
{
    Entity * e;
    {
        Entity* entity = new Entity();
        e = entity;
    }
    delete e;
}
```

使用堆分配的内存需要手动释放

new和delete在调用时不仅会调用malloc和free，还会调用构造函数和析构函数

new可以指定分配的地点

```c++
int* b = new int[100];
Entity *e = new(b) Entity();
```

就是在b的位置分配内存

## 3.20 隐式转换

C++编译器会帮你完成一些类型转换的工作

```c++
#include<iostream>
#include<string>

class Entity
{
private:
	std::string m_Name;
	int m_Age;
public:
	Entity(const std::string& name) :m_Name(name), m_Age(-1) {}

	Entity(int age) :m_Name("Unkonow"), m_Age(age) {}
};
int main()
{
	using namespace std::string_literals;
	Entity a = 20;
	Entity b = "Hello"s;//Entity b("Hello");可以
}
```

上面的例子是可行的

使用explicit就不可以这么用了

```c++
class Entity
{
private:
	std::string m_Name;
	int m_Age;
public:
	explicit Entity(const std::string& name) :m_Name(name), m_Age(-1) {}

	explicit Entity(int age) :m_Name("Unkonow"), m_Age(age) {}
};
```

## 3.21 操作符重载 Overloading

```c++
#include<iostream>
#include<string>

struct Vector2
{
	float x, y;
	Vector2(float xa, float ya) :x(xa), y(ya) {}

	Vector2 operator+ (const Vector2& other) const
	{
		return Vector2(this->x + other.x, this->y + other.y);
	}
	Vector2 operator* (const Vector2& other)const
	{
		return Vector2(this->x * other.x, this->y * other.y);
	}
};

std::ostream& operator<< (std::ostream& stream, const Vector2& vec) 
{
	stream << vec.x << "," << vec.y;
	return stream;
}

int main()
{
	Vector2 pos(1.0f, 2.0f);
	Vector2 speed(2.0f, 4.0f);
	Vector2 power(3.0f, 4.0f);
	Vector2 result = pos + speed * power;

	std::cout << result << std::endl;

	std::cin.get();
}
```

这里还做了一个<<的重载，需要注意一下

## 3.22 this 

this只能由成员访问

代表一个指向实例的指针

## 3.23 对象的lifetime

```c++
#include<iostream>

class Entity
{
public:
	Entity()
	{
		std::cout << "Create" << std::endl;
	}
	~Entity()
	{
		std::cout << "Destroyed" << std::endl;
	}
};
class ScopePtr
{
private:
	Entity* m_e;
public:
	ScopePtr(Entity* e):m_e(e)
	{
	}
	~ScopePtr()
	{
		delete m_e;
	}
};

int main()
{
	{
		ScopePtr ptr(new Entity());
	}


	std::cin.get();
}
```

虽然是用了new，没有手工释放，但是使用这个指针，也实现了自动释放

## 3.24 智能指针

不用调用new和delete，自动实现内存分配和释放

- unique

  ```c++
  #include<iostream>
  
  class Entity
  {
  public:
  	Entity()
  	{
  		std::cout << "Create" << std::endl;
  	}
  	~Entity()
  	{
  		std::cout << "Destroyed" << std::endl;
  	}
  };
  
  int main()
  {
  	{
  		std::unique_ptr<Entity> entity(new Entity());
  	}
  
  
  	std::cin.get();
  }
  ```

  也可以这么写

  ```c++
  std::unique_ptr<Entity> entity = std::make_unique<Entity>();
  ```

  这个类没有拷贝构造函数，所以没有以下的用法

  ```c++
  std::unique_ptr<Entity> e0 = entity;
  ```

- shared_ptr

  ```c++
  std::shared_ptr<Entity> entity = std::make_shared<Entity>();
  ```

  这个能为一个对象的指针指定很多reference

  ```c++
  std::shared_ptr<Entity> e0 = entity;
  ```

  并且，会有一个reference count的量，当所有的shared_ptr都消失时，这个对象的析构函数才会被调用

- weak_ptr

  这个就是不会增加reference count的值

## 3.25 Copy constructor

```c++
int a = 9;
int b = a;
b = 10;
```

这里b是a的一份拷贝值，修改b的值不影响a的值

```c++
Vector* a = new Vector2();
Vector* b = a;
```

这里其实也是一个拷贝值，只不过拷贝的是地址

当一个类的成员有指针时，我们拷贝一个类，是浅拷贝，实际上变量都拷贝了一份，指针也被拷贝了一份，但是，拷贝之后里面存的地址是相同的，所以，一个释放了之后，另一个就没地址可以释放了，这样就会错误，比如下面的代码

```c++
class A
{
private:
    char* s;
    int len;
public:
    A(char* sa, int lena)
    {
        len = strlen(sa);
        sa = new char[len+1];
        memcpy(s,sa,len+1);
    }
    A(const A& a):s(a.s),len(a.len)
    {
    }
};
```

这里需要使用深度拷贝，因为有指针变量

```c++
class A
{
private:
    char* s;
    int len;
public:
    A(char* sa, int lena)
    {
        len = strlen(sa);
        sa = new char[len+1];
        memcpy(s,sa,len+1);
    }
    A(const A& a):len(a.len)
    {
        a.s = new char[a.len+1];
        memcpy(s,a.s,len+1);
    }
};
```

注意，当传值时候，其实都有这么一个过程发生了，所以更多的时候，使用引用传值

## 3.26 Arrow Operators

首先来一个重载

```c++
#include<iostream>

class Entity
{
private:
	int x, y;
public:
	Entity() {  };
	~Entity() { };
	void Print() const {} //由于是const，所以const调用的方法也要是const
}; 

class ScopePtr
{
private:
	Entity* m_e;
public:
	ScopePtr(Entity* e):m_e(e)
	{
	}

	~ScopePtr()
	{
		delete m_e;
	}
	Entity* GetObject()
	{
		return m_e;
	}
	Entity*& operator->()
	{
		return m_e;
	}
	const Entity* operator->() const //由于不能仅凭返回值来重载，所以这里给输入也限定成const
	{
		return m_e;
	}
};

int main()
{
	ScopePtr ptr(new Entity());
	ptr.GetObject()->Print();
	//这里这么调用实在是麻烦，是否可以使用->这种访问方式直接访问Object里的方法？
	ptr->Print();
	const ScopePtr ptr2(new Entity()); //这是一个const类型的，所以相应的操作符还需要重载一次
	ptr2->Print();

	std::cin.get();
}
```

还可用来查看变量存储的结构

```c++
#include<iostream>

struct Vector3
{
	float x, y, z;
};


int main()
{
	int offset = &((Vector3*)nullptr)->x;
	std::cout << offset << std::endl;
	std::cin.get();
}
```

上面代码可以表示x在struct中存储的offset

## 3.27 Dynamic Arrays : Vectors

```c++
#include<iostream>
#include<vector>


struct Vectex
{
	float x, y, z;
};

std::ostream& operator<<(std::ostream& stream,  const Vectex& e)
{
	stream << e.x << ", " << e.y << ", " << e.z;
	return stream;
}
void Fun(const std::vector<Vectex>& vec)
{

}

int main()
{
	std::vector<Vectex> vectices;
	vectices.push_back({ 1,2,3 });
	vectices.push_back({ 4,5,6 });
	Fun(vectices);
	for (int i = 0; i < vectices.size(); i++)
		std::cout << vectices[i] << std::endl;

	for (Vectex& v : vectices) //这种写法倒是没有见过
		std::cout << v << std::endl;

	vectices.erase(vectices.begin() + 1);
	vectices.clear();


	std::cin.get();
}
```

下面再来看一个例子

```c++
#include<iostream>
#include<vector>


struct Vectex
{
	float x, y, z;
	Vectex(float xa, float ya, float za) :x(xa), y(ya), z(za)
	{
		std::cout << "Create!" << std::endl;
	}
	Vectex(const Vectex& e):x(e.x),y(e.y),z(e.z)
	{
		std::cout << "Copy!" << std::endl;
	}
};

std::ostream& operator<<(std::ostream& stream,  const Vectex& e)
{
	stream << e.x << ", " << e.y << ", " << e.z;
	return stream;
}
void Fun(const std::vector<Vectex>& vec)
{

}

int main()
{
	std::vector<Vectex> vectices;
	vectices.push_back({ 1,2,3 });
	vectices.push_back({ 4,5,6 });
	vectices.push_back({ 7,8,9 });

	std::cin.get();
}
```

输出结果是

```bash
Create!
Copy!
Create!
Copy!
Copy!
Create!
Copy!
Copy!
Copy!
```

Why? 首先不用怀疑的是，每一次push_back，都是在main（）的栈中构建一个Vectex，然后这个要想到入到Vector中，就得被copy一次，所以有了一次Create和一次Copy，但是第二次的时候，仍然是要Create一次，Vector需要将原先的都拷贝到另外一个地址中，来得到更多的空间，所以这里会发生两次Copy，同理第三次

如何避免这种需要分配？

```c++
int main()
{
	std::vector<Vectex> vectices;
	vectices.reserve(3);
	vectices.push_back({ 1,2,3 });
	vectices.push_back({ 4,5,6 });
	vectices.push_back({ 7,8,9 });

	std::cin.get();
}
```

```bash
Create!
Copy!
Create!
Copy!
Create!
Copy!
```

使用reserve时，可以指定分配的内存空间的大小，可以看到，每次都只拷贝了当前的，没有全员拷贝、

那我连一次都不想被拷贝咋办？

```c++
#include<iostream>
#include<vector>


struct Vectex
{
	float x, y, z;
	Vectex(float xa, float ya, float za) :x(xa), y(ya), z(za)
	{
		std::cout << "Create!" << std::endl;
	}
	Vectex(const Vectex& e):x(e.x),y(e.y),z(e.z)
	{
		std::cout << "Copy!" << std::endl;
	}
	Vectex(float xa) :x(xa), y(1), z(3)
	{
		std::cout << "Create2!" << std::endl;
	}
};

std::ostream& operator<<(std::ostream& stream,  const Vectex& e)
{
	stream << e.x << ", " << e.y << ", " << e.z;
	return stream;
}
void Fun(const std::vector<Vectex>& vec)
{

}

int main()
{
	std::vector<Vectex> vectices;
	vectices.reserve(3);
	vectices.emplace_back( 1,2,3 );
	vectices.emplace_back(4,5,6 );
	vectices.emplace_back(7);

	std::cin.get();
}
```

```bash
Create!
Create!
Create2!
```

使用emplace_back可以做到，这里是传了一个参数表给它，然后它再调用构造函数，这样复制的就不是这个对象了

## 3.28 Library

- static linking: 将库导入到你的可执行程序中

  其实就是将下载的文件包含过来，VS在项目属性中->C/C++->常规->附加包含目录里可以添加文件目录，这里一般会使用到一些宏，比如${SolutionDir}表示当前解决方案的目录，添加的路径是头文件路径

  然后要使用时，只需要在文件中inlcude相应的库文件就好了

  但是这仍然没有提供相关库的定义文件，只有头文件，还需要在项目属性->连接器->常规中添加库目录，这个和前面添加头文件是类似的方法，还需要在输入的添加依赖项中添加相应的依赖,就是lib的文件名，这个才是依赖文件，上面添加的是路径

  ```c++
  extern "C" int glfwInit();
  ```

  如果是C的库，需要这么引用函数，当然用头文件就不用这么做了

  ```c++
  #include <iostream>
  #include <GLFW/glfw3.h>
  
  int main()
  {
      int a = glfwInit();
      std::cout<<a<<std:;endl;
      
      std::cin.get();
  }
  ```

- dynamic linking: 在运行时链接，dll文件

  代码方面基本一样，需要将静态库的依赖改成动态库依赖，然后将dll文件放到exe文件目录下面，就可以使用了。
  
- 静态链接可以单独运行一个exe，然而动态链接需要相关的依赖项

## 3.29 VS多项目

造自己的库文件...

在VS的一个项目右击，添加新项目就可以，然后可以在项目属性->常规->选择配置类型。点击生成的时候，生成的就不是exe了，而是你选择的库类型文件。

然后也就和上面一节中类似的使用方法了，当然，由于两个项目在同一个解决方案中，所以直接点击项目->添加->引用，也可以添加。而且后者可以避免忘记修改库之后忘记编译的问题，因为编译器会帮你做，前者需要手动编译。

## 3.30 如何返回多个值

- 可以使用引用传参，传入需要返回的值即可，这种方法很高效，当然也可以传指针

- 使用array的方式返回

- 上面两种方式返回的都是同一种类型，返回不同类型时，可以使用tuple

  ```c++
  #include <tuple>
  std::tuple<std::string,std::string,int> fun()
  {
      std::tuple<std::string,std::string,int> a;
      return a;
  }
  ```

- 当然，一个比较简单，可读的解决方式是使用struct

## 3.31 模板

```c++
template<typename T>
void Print(T value)
{
    std::cout<<value<<std::endl;
}

int main()
{
    Print(5);
    Print<float>(5.5f); //尖括号可以省去，隐式的转换
    Print("Hello"); //为什么这里可以直接转换成std::string啊
}
```

也可以使用class代替typename

模板函数只有在被调用的时候才会被创造，在之前都是不存在的，所以编译不会通不过，甚至不会报错（在有错误的情况下）

```c++
class Array
{
private:
    int m_Array[N];  
public:
    int GetSize() const { return N; }
};
int main()
{
    
    Array<5> a;
    std::cout<<a.GetSize()<<std::endl;

    std::cin.get();
}
```

```c++
template<typename T,int N>
class Array
{
private:
    T m_Array[N];
public:
    int GetSize() const { return N; }
};
int main()
{
    
    Array < std::string , 5 > a;
    std::cout<<a.GetSize()<<std::endl;

    std::cin.get();
}
```

## 3.32 宏

```c++
#define LOG(x) std::cout<<x<<std::endl;
```

```c++
#ifdef PR_DEBUG
#define LOG(x) std::cout<<x<<std:;endl;
#else
#define LOG(x)
```

```c++
#define MAIN int main()\
{\
	std::cin.get();\
}
```

## 3.33 别名

```c++
using String = std::string;
typedef std::string String;
```

如果变量类型太长，可以使用auto关键字定义变量。

## 3.34 std::array

```c++
std::array<int, 5> a;
a.size();
```

和其它容器一样有很多的迭代器

在较高级别的debug_level下，会进行越界检查

## 3.35 函数指针

```c++
void HelloWorld()
{
    std::cout << "Hello World" << std::endl;
}


int main()
{
    auto function = HelloWorld; //这里本来是&HelloWorld， 但是有一个隐式转换
    function();
    

    std::cin.get();
}
```

```c++
void HelloWorld(int a)
{
    std::cout << "Hello World with data " <<a<< std::endl;
}


int main()
{
    void(*function)(int) = HelloWorld;
    function(8);

    std::cin.get();
}
```

```c++
void HelloWorld(int a)
{
    std::cout << "Hello World with data " <<a<< std::endl;
}


int main()
{
    typedef void(*function)(int);//在这里类型名是void (*) (int) ，别名是 function

    function Fun = HelloWorld;
    Fun(8);

    std::cin.get();
}
```

## 3.36 lambdas

```c++
[capture list] (parameter list) -> return type {function body}
```

- 捕获列表：当要使用函数体内的变量时，要使用这个

  ```c++
  [a,&b]
  [&] : by reference
  [=] : all automatic variables and objects by  copy
  ```

  其他的就和一个函数指针一样

- 除了捕获列表和函数体，其余的可以省略

```c++
void ForEach(const std::vector<int>& values, int(*func)(int))
{
    for (int value : values)
    {
        func(value);
    }
}

int Print(int a)
{
    return a;
}
int main()
{
    std::vector<int> values = { 1,2,3,4,5 };
    auto a = [](int value) ->int { std::cout << value << std::endl; return value; };
    ForEach(values, a);
    auto c = Print;
    ForEach(values, c);
    auto b = [](int var)->int {return var; };
    std::cout << b(5) << std::endl;

    std::cin.get();
}
```

```c++
#include<iostream>
#include<vector>

int main()
{
    std::vector<int> values = { 1,2,3,4,5 };
    auto a = std::find_if(values.begin(), values.end(), [](int value) {return value > 3; });

    std::cout << *a << std::endl;

    std::cin.get();
}
```

## 3.37 namespace

```c++
namespace apple
{
    void Print(){}
}
namespace orange
{
    void Print(){}
}

int main()
{
    apple::Print();
    namespace b = orange;
    b::Print();
}
```

可以避免命名冲突

## 3.38 线程

可以简单理解成很多事一起做？

- join() :just like wait,本来是多个线程，但是现在join之后，表示等待这个线程完成再继续main thread

```c++
#include<iostream>
#include<thread>

static bool s_Finished= false;
void DoWork()
{
    using namespace std::literals::chrono_literals;
    while (!s_Finished)
    {
        std::cout << "Work" << std::endl;
        std::this_thread::sleep_for(1s);
    }
}

int main()
{
    std::thread worker(DoWork);
    std::cin.get();
    s_Finished = true;
    worker.join();

    std::cin.get();
}
```

## 3.39 timing

```c++
#include<iostream>
#include<thread>
#include<chrono>


int main()
{
    using namespace std::literals::chrono_literals;
    auto start = std::chrono::high_resolution_clock::now();
    std::this_thread::sleep_for(1s);
    auto end = std::chrono::high_resolution_clock::now();
    
    std::chrono::duration<float> duration = end - start;

    std::cout << duration.count() << std::endl;
    
    
    std::cin.get();
}
```

```c++
#include<iostream>
#include<thread>
#include<chrono>


struct Timer
{
     std::chrono::steady_clock::time_point  start = std::chrono::high_resolution_clock::now();
     std::chrono::steady_clock::time_point  end = std::chrono::high_resolution_clock::now();
     std::chrono::duration<float>  duration;


    Timer()
    {
        start = std::chrono::high_resolution_clock::now();
    }
    ~Timer()
    {
        end = std::chrono::high_resolution_clock::now();
        duration = end - start;
        std::cout << duration.count() << std::endl;
    }
};

void Function()
{
    Timer t;
    for (int i = 0; i < 100; i++)
    {
        std::cout << "Hello" << std::endl;
    }
}

int main()
{
  
    Function();
    std::cin.get();
}
```

## 3.40 std::sort

```c++
#include<iostream>
#include<vector>
#include<algorithm>
#include<functional>
int main()
{
    std::vector<int> values = { 3,2,5,4,1 };
    std::sort(values.begin(), values.end(),std::greater<int>()); //默认是升序，这样是降序
    std::sort(values.begin(), values.end(), [](int a, int b) {return a > b; }); //默认是升序，这样是降序

    for (int value : values)
        std::cout << value << std::endl;
    std::cin.get();
}
```

不会的参考[这个网站](https://en.cppreference.com/w/)

## 3.41 Union

共用一块内存

共用体内可能有很多的变量，但是它们都是一块内存，至此就会涉及到类型双关的问题。

比如

```c++
struct Union
{
    union
    {
        float x;
        int y;
    };
};
Union u;
u.x = 2;
std::cout<<u.x<<" "<<u.y<<std::endl;
```

```c++
2 1073741824
```

第二个数字正是00000002在float计数方式中的数值

```c++
#include<iostream>

struct Vector2
{
    float x, y;
};
struct Vector4
{
    union
    {
        struct
        {
            float x, y, z, w;
        };
        struct
        {
            Vector2 a, b;
        };
    };
};

std::ostream& operator<<(std::ostream& stream, const Vector2 vec)
{
    stream << vec.x << ", " << vec.y << std::endl;
    return stream;
}

int main()
{
    Vector4 v = { 1,2,3,4 };
    std::cout << v.a << std::endl;
    std::cout << v.b << std::endl;

    std::cin.get();
}
```

## 3.42 虚析构函数

```
#include<iostream>

class Base
{
public:
    Base()
    {
        std::cout << "Base Constuctor" << std::endl;
    }
    ~Base()
    {
        std::cout << "Base Destuctor" << std::endl;
    }
};

class Derived :public Base
{
public:
    Derived()
    {
        std::cout << "Derived Constuctor" << std::endl;
    }
    ~Derived()
    {
        std::cout << "Derived Destuctor" << std::endl;
    }
};

int main()
{
    Base* a = new Base();
    delete a;

    std::cout << "=========================" << std::endl;

    Derived* b = new Derived();
    delete b;

    std::cin.get();
}
```

```bash
Base Constuctor
Base Destuctor
=========================
Base Constuctor
Derived Constuctor
Derived Destuctor
Base Destuctor
```

派生类销毁时也会调用基类的析构函数，构造时也会先调用基类的构造函数

```c++
int main()
{
    Base* a = new Base();
    delete a;

    std::cout << "=========================" << std::endl;

    Derived* b = new Derived();
    delete b;

    std::cout << "=========================" << std::endl;

    Base* c = new Derived();
    delete c;

    std::cin.get();
}
```

```bash
Base Constuctor
Base Destuctor
=========================
Base Constuctor
Derived Constuctor
Derived Destuctor
Base Destuctor
=========================
Base Constuctor
Derived Constuctor
Base Destuctor
```

不像虚方法，析构函数改成虚函数，会同时调用两个析构函数

