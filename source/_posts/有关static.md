---
title: 有关static
date: 20200519 13:04:11
tags: [C++]
---

有关C++中static关键字的一些用法

<!--more-->



# 1. 用于函数、变量的声明

在一个文件中定义了一个函数，然后要在另一个文件中使用这个变量？这个时候最常见的方法就是在需要使用的那个文件中声明一下这个变量

```c++
extern int a;
```

表示这个文件中使用的a来自于另一个文件中定义的全局变量。

如果不加extern关键字，就会有linking error，因为找到了两个a变量，有冲突。

那么如何让这个变量在其他文件中不可见，而只在此文件中有文件作用域呢？

使用static关键字即可。

```c++
static int a;
```

这样其他文件中再

```c++
int a;
```

也不会产生冲突。

函数也是同理,static函数只会在当前文件作用域中有效，其他文件无法使用。如果是普通的函数，其他文件使用只需加上声明就可以有linker链接过去了。

这里有一个很有意思的例子

当在一个文件中写下面这段代码时

```c++
int fun();

int fun1()
{
    fun();
    return 0;
}

int main(void)
{
    fun1();
}
```

仅编译这个文件时，可以通过编译，虽然此文件中没有定义fun()，但是给了声明，编译器可以通过这个单元的编译。但是如果在整个程序中都没有这个函数的定义，那么单文件依然可以编译，但是链接会出错,因为没有fun（）的定义。

如果将上面的代码改动一下

```c++
int fun();

int fun1()
{
    //fun();
    return 0;
}

int main(void)
{
    fun1();
}
```

此时调用fun1()时不再调用fun()，此时即使没有给fun()的定义也并不影响链接，所以可以通过编译链接。

但是如果改成下面这个代码

```c++
int fun();

int fun1()
{
    fun();
    return 0;
}

int main(void)
{
    //fun1();
}
```

此时，虽然程序不调用fun1()了，也就不会用到fun()，但是链接还是错误！这不是和上面的是矛盾的吗？并不，因为虽然这个文件中没有用到fun1()，但是fun1()可能被其他文件调用，因为fun1()的形式以及决定了，它要运行就必须有fun()的定义。

但是做了如下更改后，却发现又可以通过了

```c++
int fun();

static int fun1()
{
    fun();
    return 0;
}

int main(void)
{
    //fun1();
}
```

此时，虽然还是没有定义fun()，但是fun1()是静态的，不在此文件中使用，也不可能被其他文件使用，这时就没有链接错误了。

# 2. class 和 struct中的static

class和struct中的static是很有意思的，也像其他的静态变量一样，代表这个变量或函数在这个空间域里是只有一份的。举个例子

```c++
class student
{
public:
    static int count;
    
    ...
};

int student::count;//全局定义一下这个变量
```

在这个例子中，所有的student实例都是公用一个count的，对其中一个count操作，对其他的也会有影响。

方法（即类里的函数)也是一样。

还可以发现，这种变量和函数都是不依赖于具体的实例的，所以可以直接通过类来调用这种变量和函数，实际上它们也就是这么工作的。

```c++
student student1;
student1.count ++;
```

上面的第二行代码，在实际运行中是以下面的方式运行的，而不是通过实例来调用

```c++
student::count++;
```

当然，可以直接使用上面的方式写代码，使用类名引用。

静态数据成员可以被初始化，但是只能在类体外进行初始化。当然在构造一个对象时也不能给它赋在参数表中。

**使用static时要在全局定义这个变量**,否则是无法解析这个符号的linking error，因为它在类声明中只能声明，没有分配空间，所以需要分配它的空间。（这个并不确定，因为貌似在编译时会被分配空间，但是这样貌似解释不通）

> 全局变量的声明是通过extern关键字的

需要注意的是，由于这种方法和变量都不依赖于实例，自然这种方法不能操作具体实例里的非静态变量。



# 3. 普通的用法

用在函数中，多次调用这个函数时这样量不会重新分配空间，运行结束也不会销毁。

强行变长了生命周期

```c++
class Singleton
{
    static Singleton* s_Instance;
public:
	static Singleton& get()
    {
        return *s_Instance;
	}
    void Hello(){}
};
Singleton* s_Instance = nullptr;
```

这里返回一个实例:单例设计模式，即这个类只有一个实例

可以修改

```c++
class Singleton
{
public:
    static Singleton& get()
    {
        static Singleton Instance;
        return Instance;
    }
};
```

这里如果不加static，则Instance返回时就没有了，自然也就不能引用这个变量了

当然可以不使用引用，这样返回一个Instance的复制也可以

```c++
class Singleton
{
public:
    static Singleton get()
    {
        Singleton Instance;
        return Instance;
    }
};
```





