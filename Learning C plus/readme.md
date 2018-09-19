# C++学习之路

> 借鉴 [C++教程 by 菜鸟教程](http://www.runoob.com/cplusplus/cpp-tutorial.html)
>
> 由于我们在本科阶段已经学习了C语言的基础知识，同时我也翻阅了该教程第一部分的内容，确实没有难于理解的地方，因此我直接进入第二部分关于面向对象的学习。

[TOC]



## C++面向对象（OOP）

### C++类和对象简介

#### C++ 类定义

类是 C++ 的核心特性，通常被称为用户定义的类型。 但是结构体同样是用户自定义的数据类型，那么他们的[区别](https://blog.csdn.net/fu_zk/article/details/13022921)是什么？好吧，感觉区别不大，但是至少博主还对面向对象的特点进行了总结。

类定义是以关键字 **class** 开头，后跟类的名称。类的主体是包含在一对花括号中。类定义后必须跟着一个分号或一个声明列表。

```c++
class Box
{
   public:
      double length;   // 盒子的长度
      double breadth;  // 盒子的宽度
      double height;   // 盒子的高度
};
```

关键字 **public** 确定了类成员的访问属性。在类对象作用域内，公共成员在类的外部是可访问的。

C++还提供了很多来自类库的类，例如`iostream`中的`ostream`和`istream`。

#### 定义 C++ 对象

**类之于对象相当于类型之于变量。类定义描述的是数据格式及其用法，而对象则是根据数据格式规范创建实体。**类描述了一种数据类型的全部属性（包括可使用它的操作），对象是根据这些描述创建实体。

```c++
Box Box1;          // 声明 Box1，类型为 Box
Box Box2;          // 声明 Box2，类型为 Box
```

------

### C++ 类成员函数

类的成员函数是指那些把定义和原型写在类定义内部的函数，就像类定义中的其他变量一样。类成员函数是类的一个成员，它可以操作类的任意对象，可以访问对象中的所有成员。

```c++
class Box
{
   public:
      double length;         // 长度
      double breadth;        // 宽度
      double height;         // 高度
      double getVolume(void);// 返回体积 ，此即为类成员函数
};
```

**两种方式定义类成员函数：**

1. 直接定义在类定义内部。在类定义中定义的成员函数把函数声明为[**内联**](https://www.cnblogs.com/QG-whz/p/4641479.html)的，即便没有使用 inline 标识符。

   ```c++
   class Box
   {
      public:
         double length;      // 长度
         double breadth;     // 宽度
         double height;      // 高度
      
         double getVolume(void)
         {
            return length * breadth * height;
         }
   };
   ```

1. 在类的外部使用**范围解析运算符 ::** 。

   ```c++
   double Box::getVolume(void)
   {
       return length * breadth * height;
   }
   ```

调用成员函数：在对象上使用点运算符（**.**）

```c++
Box myBox;          // 创建一个对象
myBox.getVolume();  // 调用该对象的成员函数
```

------

### C++ 类访问修饰符

**数据封装**是面向对象编程的一个重要特点，它防止函数直接访问类类型的内部成员。**这是C++实现高内聚、低耦合的关键。**类成员的访问限制是通过在类主体内部对各个区域标记 `public`、`private`、`protected` 来指定的。关键字 `public`、`private`、`protected` 称为访问修饰符。

```c++
class Base {
 
   public:
  // 公有成员
 
   protected:
  // 受保护成员
 
   private:
  // 私有成员
 
};
```

#### 公有（public）成员

**公有**成员在程序中类的外部是可访问的。可以不使用任何成员函数来设置和获取公有变量的值。

#### 私有（private）成员

**私有**成员变量或函数在类的外部是不可访问的，甚至是不可查看的。只有类和友元函数可以访问私有成员。**默认情况下，类的所有成员都是私有的。**实际操作中，我们一般会**在私有区域定义数据**，**在公有区域定义相关的函数**，以便在类的外部也可以调用这些函数。

```c++
#include <iostream>
 
using namespace std;
 
class Box
{
   public:
      double length;
      void setWidth( double wid );
      double getWidth( void );
 
   private:
      double width;
};
 
// 成员函数定义
double Box::getWidth(void)
{
    return width ;
}
 
void Box::setWidth( double wid )
{
    width = wid;
}
 
// 程序的主函数
int main( )
{
   Box box;
 
   // 不使用成员函数设置长度
   box.length = 10.0; // OK: 因为 length 是公有的
   cout << "Length of box : " << box.length <<endl;
 
   // 不使用成员函数设置宽度
   // box.width = 10.0; // Error: 因为 width 是私有的
   box.setWidth(10.0);  // 使用成员函数设置宽度
   cout << "Width of box : " << box.getWidth() <<endl;
 
   return 0;
}
```

#### 保护（protected）成员

**保护**成员变量或函数与私有成员十分相似，但有一点不同，保护成员在派生类（即子类）（接下来才能学到）中是可访问的。

```c++
#include <iostream>
using namespace std;
 
class Box
{
   protected:
      double width;
};
 
class SmallBox:Box // SmallBox 是派生类
{
   public:
      void setSmallWidth( double wid );
      double getSmallWidth( void );
};
 
// 子类的成员函数
double SmallBox::getSmallWidth(void)
{
    return width ; // 此处访问了父类中的保护成员
}
 
void SmallBox::setSmallWidth( double wid )
{
    width = wid;  // 此处访问了父类中的保护成员
}
 
// 程序的主函数
int main( )
{
   SmallBox box;
 
   // 使用成员函数设置宽度
   box.setSmallWidth(5.0);
   cout << "Width of box : "<< box.getSmallWidth() << endl;
 
   return 0;
}
```

------

### 继承中的特点

有`public`, `protected`, `private`三种继承方式，它们相应地改变了基类成员的访问属性。

- **public 继承：**基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：public, protected, private

- **protected 继承：**基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：protected, protected, private

- **private 继承：**基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：private, private, private

  由上述可知，`private`是权限要求最高的一种继承。但无论哪种继承方式，上面两点都没有改变：

  - private 成员只能被本类成员（类内）和友元访问，不能被派生类访问；
  - protected 成员可以被派生类访问。


**public 继承**

```c++
#include<iostream>
#include<assert.h>
using namespace std;
 
class A{
public:
  int a;
  A(){
    a1 = 1;
    a2 = 2;
    a3 = 3;
    a = 4;
  }
  void fun(){
    cout << a << endl;    //正确
    cout << a1 << endl;   //正确
    cout << a2 << endl;   //正确
    cout << a3 << endl;   //正确
  }
public:
  int a1;
protected:
  int a2;
private:
  int a3;
};
class B : public A{
public:
  int a;
  B(int i){
    A();
    a = i;
  }
  void fun(){
    cout << a << endl;       //正确，public成员 
    cout << a1 << endl;       //正确，基类的public成员，在派生类中仍是public成员。
    cout << a2 << endl;       //正确，基类的protected成员，在派生类中仍是protected可以被派生类访问。
    cout << a3 << endl;       //错误，基类的private成员不能被派生类访问。
  }
};
int main(){
  B b(10);
  cout << b.a << endl;
  cout << b.a1 << endl;   //正确
  cout << b.a2 << endl;   //错误，类外不能访问protected成员
  cout << b.a3 << endl;   //错误，类外不能访问private成员
  system("pause");
  return 0;
}
```

**protected 继承**

```C++
#include<iostream>
#include<assert.h>
using namespace std;
class A{
public:
  int a;
  A(){
    a1 = 1;
    a2 = 2;
    a3 = 3;
    a = 4;
  }
  void fun(){
    cout << a << endl;    //正确
    cout << a1 << endl;   //正确
    cout << a2 << endl;   //正确
    cout << a3 << endl;   //正确
  }
public:
  int a1;
protected:
  int a2;
private:
  int a3;
};
class B : protected A{
public:
  int a;
  B(int i){
    A();
    a = i;
  }
  void fun(){
    cout << a << endl;       //正确，public成员。**此处有疑问，为什么a和a1不一样**
    cout << a1 << endl;       //正确，基类的public成员，在派生类中变成了protected，可以被派生类访问。
    cout << a2 << endl;       //正确，基类的protected成员，在派生类中还是protected，可以被派生类访问。
    cout << a3 << endl;       //错误，基类的private成员不能被派生类访问。
  }
};
int main(){
  B b(10);
  cout << b.a << endl;       //正确。public成员
  cout << b.a1 << endl;      //错误，protected成员不能在类外访问。
  cout << b.a2 << endl;      //错误，protected成员不能在类外访问。
  cout << b.a3 << endl;      //错误，private成员不能在类外访问。
  system("pause");
  return 0;
}
```

**private 继承**

```
#include<iostream>
#include<assert.h>
using namespace std;
class A{
public:
  int a;
  A(){
    a1 = 1;
    a2 = 2;
    a3 = 3;
    a = 4;
  }
  void fun(){
    cout << a << endl;    //正确
    cout << a1 << endl;   //正确
    cout << a2 << endl;   //正确
    cout << a3 << endl;   //正确
  }
public:
  int a1;
protected:
  int a2;
private:
  int a3;
};
class B : private A{
public:
  int a;
  B(int i){
    A();
    a = i;
  }
  void fun(){
    cout << a << endl;       //正确，public成员。
    cout << a1 << endl;       //正确，基类public成员,在派生类中变成了private,可以被派生类访问。
    cout << a2 << endl;       //正确，基类的protected成员，在派生类中变成了private,可以被派生类访问。
    cout << a3 << endl;       //错误，基类的private成员不能被派生类访问。
  }
};
int main(){
  B b(10);
  cout << b.a << endl;       //正确。public成员
  cout << b.a1 << endl;      //错误，private成员不能在类外访问。
  cout << b.a2 << endl;      //错误, private成员不能在类外访问。
  cout << b.a3 << endl;      //错误，private成员不能在类外访问。
  system("pause");
  return 0;
}
```

------

### C++ 类构造函数 & 析构函数

#### 类的构造函数

类的**构造函数**是类的一种特殊的成员函数，它会在每次**创建**类的新对象时执行。

构造函数的名称与类的名称是完全相同的，并且不会返回任何类型，也不会返回 void。构造函数可用于为某些成员变量设置初始值。

```c++
#include <iostream>
 
using namespace std;
 
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line();  // 这是构造函数
 
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
 
void Line::setLength( double len )
{
    length = len;
}
 
double Line::getLength( void )
{
    return length;
}
// 程序的主函数
int main( )
{
   Line line;
 
   // 设置长度
   line.setLength(6.0); 
   cout << "Length of line : " << line.getLength() <<endl;
 
   return 0;
}
```

默认的构造函数没有任何参数，但如果需要，构造函数也**可以带有参数**。这样在创建对象时就会给对象赋初始值

```c++
...
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line(double len);  // 这是构造函数，带参数
 
   private:
      double length;
};
// 成员函数定义，包括构造函数
Line::Line( double len)
{
    cout << "Object is being created, length = " << len << endl;
    length = len;
}
...
```

#### 类的析构函数

类的**析构函数**是类的一种特殊的成员函数，它会在每次**删除**所创建的对象时执行。

析构函数的名称与类的名称是完全相同的，只是在前面加了个**波浪号（~）**作为前缀，它不会返回任何值，也不能带有任何参数。析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。

```c++
...
class Line
{
   public:
      void setLength( double len );
      double getLength( void );
      Line();   // 这是构造函数声明
      ~Line();  // 这是析构函数声明
 
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
 ...
```

------

### C++ 拷贝构造函数

待续。。。
