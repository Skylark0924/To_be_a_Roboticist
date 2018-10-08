# C++学习之路

> 借鉴 [C++教程 by 菜鸟教程](http://www.runoob.com/cplusplus/cpp-tutorial.html)
>
> 由于我们在本科阶段已经学习了C语言的基础知识，同时我也翻阅了该教程第一部分的内容，确实没有难于理解的地方，因此我直接进入第二部分关于面向对象的学习。



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
使用同一类中之前创建的对象来初始化新创建的对象，通常用于：

- 通过使用另一个同类型的对象来初始化新创建的对象。
- 复制对象把它作为参数传递给函数。
- 复制对象，并从函数返回这个对象。

没懂这是要干嘛。。。看另一篇博客[试试](https://blog.csdn.net/lwbeyond/article/details/6202256)。

所以，拷贝构造函数就相当于普通类型变量的下述操作，但是鉴于对象的复杂性，下述操作需要依赖拷贝构造函数来完成：

```c++
int a = 100;
int b = a; 
```

一般拷贝构造函数会由编译器自动产生，其他的要求见[此博客](https://blog.csdn.net/lwbeyond/article/details/6202256)这个看起来比较无趣，暂且搁置。。。

------

### C++ 友元函数

类的友元函数是**定义在类外部**，但**有权访问类的所有私有（private）成员和保护（protected）成员**。尽管友元函数的原型有在类的定义中出现过，但是友元函数并不是成员函数。

友元可以是一个函数，该函数被称为友元函数；友元也可以是一个类，该类被称为友元类，在这种情况下，整个类及其所有成员都是友元。

如果要声明函数为一个类的友元，需要在类定义中该函数原型前使用关键字 **friend**，如下所示：

```c++
class Box
{
   double width;
public:
   double length;
   friend void printWidth( Box box ); // 友元函数
   void setWidth( double wid );
};
```

------

### C++ 内联函数

之前提到过。C++ [**内联函数**](https://www.cnblogs.com/QG-whz/p/4641479.html#_label2)是通常与类一起使用。如果一个函数是内联的，那么在编译时，编译器会把该函数的代码副本放置在每个调用该函数的地方。

如果想把一个函数定义为内联函数，则需要在函数名前面放置关键字 **inline**，在调用函数之前需要对函数进行定义。在类定义中的定义的函数都是内联函数，即使没有使用 **inline** 说明符。

```c++
#include <iostream>
 
using namespace std;

inline int Max(int x, int y)
{
   return (x > y)? x : y;
}

// 程序的主函数
int main( )
{

   cout << "Max (20,10): " << Max(20,10) << endl;
   cout << "Max (0,200): " << Max(0,200) << endl;
   cout << "Max (100,1010): " << Max(100,1010) << endl;
   return 0;
}
```

内联函数的意义在于，**当某些函数在程序中需要被频繁调用时，其调用函数的花销会很大**（当遇到普通函数的调用指令时，程序会保存当前函数的执行现场，将函数中的局部变量以及函数地址压入堆栈，然后再将即将调用的新函数加载到内存中，这要经历复制参数值、跳转到所调用函数的内存位置、执行函数代码、存储函数返回值等过程，当函数执行完后，再获取之前正在调用的函数的地址，回去继续执行那个函数，运行时间开销简直太多了。）**因此，C++使用内联函数替代函数调用的操作，通过inline声明，编译器首先在函数调用处使用函数体本身语句替换了函数调用语句，然后编译替换后的代码，不需要跳转到内存其他地址去执行函数调用，也不需要保留函数调用时的现场数据。**

------

### C++ this 指针

每一个对象都能通过 **this** 指针来**访问自己的地址**。**this** 指针是所有成员函数的隐含参数。因此，在成员函数内部，它可以用来指向调用对象。

注：友元函数没有 **this** 指针，因为友元不是类的成员。只有成员函数才有 **this** 指针。

```c++
#include <iostream>
 
using namespace std;
 
class Box
{
   public:
      // 构造函数定义
      Box(double l=2.0, double b=2.0, double h=2.0)
      {
         cout <<"Constructor called." << endl;
         length = l;
         breadth = b;
         height = h;
      }
      double Volume()
      {
         return length * breadth * height;
      }
      int compare(Box box)
      {
         return this->Volume() > box.Volume();
      }
   private:
      double length;     // Length of a box
      double breadth;    // Breadth of a box
      double height;     // Height of a box
};
 
int main(void)
{
   Box Box1(3.3, 1.2, 1.5);    // Declare box1
   Box Box2(8.5, 6.0, 2.0);    // Declare box2
 
   if(Box1.compare(Box2)) // this 指向Box1, if true Box1.Volume() > Box2.Volume()
   {
      cout << "Box2 is smaller than Box1" <<endl;
   }
   else
   {
      cout << "Box2 is equal to or larger than Box1" <<endl;
   }
   return 0;
}
```

------

### C++ 指向类的指针

一个指向 C++ 类的指针与指向结构的指针类似，访问指向类的指针的成员，需要使用成员访问运算符 **->**，就像访问指向结构的指针一样。与所有的指针一样，您必须在使用指针之前，对指针进行初始化。

下面的实例有助于更好地理解指向类的指针的概念：

```c++
#include <iostream>
 
using namespace std;

class Box
{
   public:
      // 构造函数定义
      Box(double l=2.0, double b=2.0, double h=2.0)
      {
         cout <<"Constructor called." << endl;
         length = l;
         breadth = b;
         height = h;
      }
      double Volume()
      {
         return length * breadth * height;
      }
   private:
      double length;     // Length of a box
      double breadth;    // Breadth of a box
      double height;     // Height of a box
};

int main(void)
{
   Box Box1(3.3, 1.2, 1.5);    // Declare box1
   Box Box2(8.5, 6.0, 2.0);    // Declare box2
   Box *ptrBox;                // Declare pointer to a class.

   // 保存第一个对象的地址
   ptrBox = &Box1;

   // 现在尝试使用成员访问运算符来访问成员
   cout << "Volume of Box1: " << ptrBox->Volume() << endl;

   // 保存第二个对象的地址
   ptrBox = &Box2;

   // 现在尝试使用成员访问运算符来访问成员
   cout << "Volume of Box2: " << ptrBox->Volume() << endl;
  
   return 0;
}
```

------

### C++ 类的静态成员

使用 **static** 关键字来把类成员定义为静态的。当我们声明类的成员为静态时，这意味着无论创建多少个类的对象，静态成员都只有一个副本。

静态成员在类的所有对象中是共享的。如果不存在其他的初始化语句，在创建第一个对象时，所有的静态数据都会被**初始化为零**。我们**不能把静态成员的初始化放置在类的定义中**，但是可以**在类的外部通过使用范围解析运算符 :: 来重新声明静态变量**从而对它进行初始化：

```c++
#include <iostream>
 
using namespace std;
 
class Box
{
   public:
      static int objectCount; // 静态成员
      // 构造函数定义
      Box(double l=2.0, double b=2.0, double h=2.0)
      {
         cout <<"Constructor called." << endl;
         length = l;
         breadth = b;
         height = h;
         // 每次创建对象时增加 1
         objectCount++;
      }
      double Volume()
      {
         return length * breadth * height;
      }
   private:
      double length;     // 长度
      double breadth;    // 宽度
      double height;     // 高度
};
 
// 初始化类 Box 的静态成员
int Box::objectCount = 0;
 
int main(void)
{
   Box Box1(3.3, 1.2, 1.5);    // 声明 box1
   Box Box2(8.5, 6.0, 2.0);    // 声明 box2
 
   // 输出对象的总数
   cout << "Total objects: " << Box::objectCount << endl;
 
   return 0;
}
```

#### 静态成员函数

如果把函数成员声明为静态的，就可以把函数与类的任何特定对象独立开来。静态成员函数即使**在类对象不存在的情况下也能被调用**，静态函数**只要使用类名加范围解析运算符 :: 就可以访问**。**静态成员函数只能访问静态成员数据、其他静态成员函数和类外部的其他函数**。

静态成员函数与普通成员函数的区别：

- 静态成员函数没有 this 指针，只能访问静态成员（包括静态成员变量和静态成员函数）。
- 普通成员函数有 this 指针，可以访问类中的任意成员；而静态成员函数没有 this 指针。

```C++
#include <iostream>
 
using namespace std;
 
class Box
{
   public:
      static int objectCount;
      // 构造函数定义
      Box(double l=2.0, double b=2.0, double h=2.0)
      {
         cout <<"Constructor called." << endl;
         length = l;
         breadth = b;
         height = h;
         // 每次创建对象时增加 1
         objectCount++;
      }
      double Volume()
      {
         return length * breadth * height;
      }
      static int getCount()
      {
         return objectCount;
      }
   private:
      double length;     // 长度
      double breadth;    // 宽度
      double height;     // 高度
};
 
// 初始化类 Box 的静态成员
int Box::objectCount = 0;
 
int main(void)
{
  
   // 在创建对象之前输出对象的总数
   cout << "Inital Stage Count: " << Box::getCount() << endl;
 
   Box Box1(3.3, 1.2, 1.5);    // 声明 box1
   Box Box2(8.5, 6.0, 2.0);    // 声明 box2
 
   // 在创建对象之后输出对象的总数
   cout << "Final Stage Count: " << Box::getCount() << endl;
 
   return 0;
}
```

------

### C++ 继承

当创建一个类时，您不需要重新编写新的数据成员和成员函数，只需指定新建的类继承了一个已有的类的成员即可。这个已有的类称为**基类**，新建的类称为**派生类**。继承代表了 **is a** 关系。例如，哺乳动物是动物，狗是哺乳动物，因此，狗是动物，等等。

#### 基类 & 派生类

**一个类可以派生自多个类**，这意味着，它可以从多个基类继承数据和函数。定义一个派生类，我们使用一个类派生列表来指定基类。

```c++
class derived-class: access-specifier base-class
```

 `access-specifier`是`public`,`private`,`protected`中的一个，默认为`private`。`base-class`是基类

```c++
...
class Rectangle: public Shape
{
   public:
      int getArea()
      { 
         return (width * height); 
      }
};
...
```

#### 访问控制和继承
派生类可以访问基类中所有的非私有成员。因此基类成员如果不想被派生类的成员函数访问，则应在基类中声明为 private。

| 访问     | public | protected | private |
| -------- | ------ | --------- | ------- |
| 同一个类 | yes    | yes       | yes     |
| 派生类   | yes    | yes       | no      |
| 外部的类 | yes    | no        | no      |

这在上面已经学过了，但注意派生类并没有继承：

- 基类的构造函数、析构函数和拷贝构造函数。
- 基类的重载运算符。
- 基类的友元函数。

#### 继承类型

基类可以被继承为 **public、protected** 或 **private** 几种类型，但几乎不使用 **protected** 或 **private** 继承，通常使用 **public** 继承。继承规则上面也已经学过了。

#### 多继承

多继承即一个子类可以有多个父类，它继承了多个父类的特性，语法如下：

```c++
class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…
{
<派生类类体>
};
```

```c++
...
// 基类 Shape
class Shape 
{
   public:
      void setWidth(int w)
      {
         width = w;
      }
      void setHeight(int h)
      {
         height = h;
      }
   protected:
      int width;
      int height;
};
 
// 基类 PaintCost
class PaintCost 
{
   public:
      int getCost(int area)
      {
         return area * 70;
      }
};
 
// 派生类
class Rectangle: public Shape, public PaintCost
{
   public:
      int getArea()
      { 
         return (width * height); 
      }
};
...
```

------

### C++ 重载运算符和重载函数

C++ 允许在同一作用域中的某个**函数**和**运算符**指定多个定义，分别称为**函数重载**和**运算符重载**。重载声明是指一个与之前已经在该作用域内声明过的函数或方法具有相同名称的声明，但是它们的参数列表和定义（实现）不相同。当您调用一个**重载函数**或**重载运算符**时，编译器通过把您所使用的参数类型与定义中的参数类型进行比较，决定选用最合适的定义。选择最合适的重载函数或重载运算符的过程，称为**重载决策**。(有一种计算机自动联系上下文理解的意思)

#### C++ 中的函数重载

在同一个作用域内，可以声明几个功能类似的同名函数，但是这些同名函数的形式参数（指参数的个数、类型或者顺序）必须不同。您不能仅通过返回类型的不同来重载函数。

```c++
...
class printData
{
   public:
      void print(int i) {
        cout << "整数为: " << i << endl;
      }
 
      void print(double  f) {
        cout << "浮点数为: " << f << endl;
      }
 
      void print(char c[]) {
        cout << "字符串为: " << c << endl;
      }
};
...
```

上述成员函数的重载会在调用的时候，根据（）中的数值类型来判断选择其中的一个。

#### C++ 中的运算符重载

重载的运算符是带有特殊名称的函数，函数名是由关键字 operator 和其后要重载的运算符符号构成的。与其他函数一样，重载运算符有一个返回类型和一个参数列表。

```c++
Box operator+(const Box&); //加法运算符的重载
```

**对象作为参数进行传递，对象的属性使用 this运算符进行访问**

```c++
#include <iostream>
using namespace std;
 
class Box
{
   public:
 
      double getVolume(void)
      {
         return length * breadth * height;
      }
      void setLength( double len )
      {
          length = len;
      }
 
      void setBreadth( double bre )
      {
          breadth = bre;
      }
 
      void setHeight( double hei )
      {
          height = hei;
      }
      // 重载 + 运算符，用于把两个 Box 对象相加
      Box operator+(const Box& b)
      {
         Box box;
         box.length = this->length + b.length;
         box.breadth = this->breadth + b.breadth;
         box.height = this->height + b.height;
         return box;
      }
   private:
      double length;      // 长度
      double breadth;     // 宽度
      double height;      // 高度
};
// 程序的主函数
int main( )
{
   Box Box1;                // 声明 Box1，类型为 Box
   Box Box2;                // 声明 Box2，类型为 Box
   Box Box3;                // 声明 Box3，类型为 Box
   double volume = 0.0;     // 把体积存储在该变量中
 
   // Box1 详述
   Box1.setLength(6.0); 
   Box1.setBreadth(7.0); 
   Box1.setHeight(5.0);
 
   // Box2 详述
   Box2.setLength(12.0); 
   Box2.setBreadth(13.0); 
   Box2.setHeight(10.0);
 
   // Box1 的体积
   volume = Box1.getVolume();
   cout << "Volume of Box1 : " << volume <<endl;
 
   // Box2 的体积
   volume = Box2.getVolume();
   cout << "Volume of Box2 : " << volume <<endl;
 
   // 把两个对象相加，得到 Box3
   Box3 = Box1 + Box2;
 
   // Box3 的体积
   volume = Box3.getVolume();
   cout << "Volume of Box3 : " << volume <<endl;
 
   return 0;
}
```

#### 可重载运算符/不可重载运算符

**可重载**

| 双目算术运算符 | + (加)，-(减)，*(乘)，/(除)，% (取模)                        |
| -------------- | ------------------------------------------------------------ |
| 关系运算符     | ==(等于)，!= (不等于)，< (小于)，> (大于>，<=(小于等于)，>=(大于等于) |
| 逻辑运算符     | \|\|(逻辑或)，&&(逻辑与)，!(逻辑非)                          |
| 单目运算符     | + (正)，-(负)，*(指针)，&(取地址)                            |
| 自增自减运算符 | ++(自增)，--(自减)                                           |
| 位运算符       | \| (按位或)，& (按位与)，~(按位取反)，^(按位异或),，<< (左移)，>>(右移) |
| 赋值运算符     | =, +=, -=, *=, /= , % = , &=, \|=, ^=, <<=, >>=              |
| 空间申请与释放 | new, delete, new[ ] , delete[]                               |
| 其他运算符     | ()(函数调用)，->(成员访问)，,(逗号)，[](下标)                |

**不可重载**

- `.`：成员访问运算符
- `.*`,` ->*`：成员指针访问运算符
- `::`：域运算符
- `sizeof`：长度运算符
- `?:`：条件运算符
- `#`： 预处理符号

**注意：**

- 1、运算重载符不可以改变语法结构。
- 2、运算重载符不可以改变操作数的个数。
- 3、运算重载符不可以改变优先级。
- 4、运算重载符不可以改变结合性。

其他关于重载的知识，见[此链接](http://www.runoob.com/cplusplus/cpp-overloading.html)

------

### C++ 多态

多态按字面的意思就是多种形态。当类之间存在层次结构，并且类之间是通过继承关联时，就会用到多态。C++ 多态意味着调用成员函数时，会根据调用函数的对象的类型来执行不同的函数。

```c++
#include <iostream> 
using namespace std;
 
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0)
      {
         width = a;
         height = b;
      }
      int area()
      {
         cout << "Parent class area :" <<endl;
         return 0;
      }
};
class Rectangle: public Shape{
   public:
      Rectangle( int a=0, int b=0):Shape(a, b) { }
      int area ()
      { 
         cout << "Rectangle class area :" <<endl;
         return (width * height); 
      }
};
class Triangle: public Shape{
   public:
      Triangle( int a=0, int b=0):Shape(a, b) { }
      int area ()
      { 
         cout << "Triangle class area :" <<endl;
         return (width * height / 2); 
      }
};
// 程序的主函数
int main( )
{
   Shape *shape;
   Rectangle rec(10,7);
   Triangle  tri(10,5);
 
   // 存储矩形的地址
   shape = &rec;
   // 调用矩形的求面积函数 area
   shape->area();
 
   // 存储三角形的地址
   shape = &tri;
   // 调用三角形的求面积函数 area
   shape->area();
   
   return 0;
}
```

当上面的代码被编译和执行时，它会产生下列结果：

```c++
Parent class area
Parent class area
```

之所以出现这种状况，是因为area()在**被调用前**、**在程序编译期间**就已经准备好了，这就是所谓的**静态多态**，或**静态链接**，也被称为**早绑定**。

要解决这种*先定义*，在area()声明前加上 `virtual`：

```c++
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0)
      {
         width = a;
         height = b;
      }
      virtual int area()
      {
         cout << "Parent class area :" <<endl;
         return 0;
      }
};
```

此时，编译器看的是指针的内容，而不是它的类型。因此，由于 tri 和 rec 类的对象的地址存储在 *shape 中，所以会调用各自的 area() 函数。

#### 虚函数

**虚函数** 是在基类中使用关键字 **virtual** 声明的函数。在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数。

我们想要的是在程序中任意点可以根据所调用的对象类型来选择调用的函数，这种操作被称为**动态链接**，或**后期绑定**。

#### 纯虚函数

您可能想要在基类中定义虚函数，以便在派生类中重新定义该函数更好地适用于对象，但是您在基类中又不能对虚函数给出有意义的实现，这个时候就会用到纯虚函数。

**即基类中的函数不添加函数体，并将函数=0**

```c++
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0)
      {
         width = a;
         height = b;
      }
      // pure virtual function
      virtual int area() = 0;
};
```

------

### C++ 数据抽象

数据抽象是指，只向外界提供关键信息，并隐藏其后台的实现细节，即**只表现必要的信息而不呈现细节**。（低耦合性的体现）

数据抽象是一种依赖于接口和实现分离的编程（设计）技术。

在 C++ 中，我们使用**类**来定义我们自己的抽象数据类型（ADT）。

#### 设计策略

抽象把代码分离为接口和实现。所以在设计组件时，必须保持接口独立于实现，这样，如果改变底层实现，接口也将保持不变。

在这种情况下，不管任何程序使用接口，接口都不会受到影响，只需要将最新的实现重新编译即可。

------

### C++ 数据封装

**数据封装**是一种把数据和操作数据的函数捆绑在一起的机制，**数据抽象**是一种仅向用户暴露接口而把具体的实现细节隐藏起来的机制。

C++ 通过创建**类**来支持封装和数据隐藏（public、protected、private）。我们已经知道，类包含私有成员（private）、保护成员（protected）和公有成员（public）成员。默认情况下，在类中定义的所有项目都是私有的。private权限就是实现封装的一种方式。

#### 设计策略

通常情况下，我们都会设置类成员状态为私有（private），除非我们真的需要将其暴露，这样才能保证良好的**封装性**。

这通常应用于数据成员，但它同样适用于所有成员，包括虚函数。

------

### C++ 接口（抽象类）

接口描述了类的行为和功能，而不需要完成类的特定实现。

C++ 接口是使用**抽象类**（通常称为 ABC）来实现的，**如果类中至少有一个函数被声明为纯虚函数**，则这个类就是抽象类。

```c++
class Box
{
   public:
      // 纯虚函数
      virtual double getVolume() = 0;
   private:
      double length;      // 长度
      double breadth;     // 宽度
      double height;      // 高度
};
```

**目的：**为了给其他类提供一个可以继承的适当的基类。抽象类不能被用于实例化对象，它只能作为**接口**使用。

因此，如果一个 ABC 的子类需要被实例化，则必须实现每个虚函数，这也意味着 C++ 支持使用 ABC 声明接口。如果没有在派生类中重载纯虚函数，就尝试实例化该类的对象，会导致编译错误。

```c++
#include <iostream>
 
using namespace std;
 
// 基类
class Shape 
{
public:
   // 提供接口框架的纯虚函数
   virtual int getArea() = 0;
   void setWidth(int w)
   {
      width = w;
   }
   void setHeight(int h)
   {
      height = h;
   }
protected:
   int width;
   int height;
};
 
// 派生类
class Rectangle: public Shape
{
public:
   int getArea()
   { 
      return (width * height); 
   }
};
class Triangle: public Shape
{
public:
   int getArea()
   { 
      return (width * height)/2; 
   }
};
 
int main(void)
{
   Rectangle Rect;
   Triangle  Tri;
 
   Rect.setWidth(5);
   Rect.setHeight(7);
   // 输出对象的面积
   cout << "Total Rectangle area: " << Rect.getArea() << endl;
 
   Tri.setWidth(5);
   Tri.setHeight(7);
   // 输出对象的面积
   cout << "Total Triangle area: " << Tri.getArea() << endl; 
 
   return 0;
}
```

一看代码就清晰明了了

#### 设计策略

面向对象的系统可能会**使用一个抽象基类**为所有的外部应用程序提供一个适当的、通用的、标准化的**接口**。

然后，派生类通过继承抽象基类，就把所有类似的操作都继承下来，并将抽象基类中的纯虚函数在相应的派生类中实现。

------

## C++高级教程
### 动态链接库（DLL）

动态链接库（也称为DLL，即为“Dynamic Link Library”的缩写）是Microsoft Windows最重要的组成要素之一，打开Windows系统文件夹，你会发现文件夹中有很多DLL文件，Windows就是将一些主要的系统功能以DLL模块的形式实现。

动态链接库是不能直接执行的，也不能接收消息，它只是一个独立的文件，其中包含能被程序或其它DLL调用来完成一定操作的函数(方法。注：C#中一般称为“方法”)，但这些函数不是执行程序本身的一部分，而是根据进程的需要按需载入，此时才能发挥作用。

DLL只有在应用程序需要时才被系统加载到进程的虚拟空间中，成为调用进程的一部分，此时该DLL也只能被该进程的线程访问，它的[句柄](https://zh.wikipedia.org/wiki/%E5%8F%A5%E6%9F%84)可以被调用进程所使用，而调用进程的句柄也可以被该DLL所使用。在内存中，一个DLL只有一个实例，且它的编制与具体的编程语言和编译器都没有关系，所以可以通过DLL来实现混合语言编程。DLL函数中的代码所创建的任何对象（包括变量）都归调用它的线程或进程所有。

#### DLL的调用

> 在此只对用C#调用DLL的方法进行介绍，用于C#、C++混合编程
>
> 借鉴[用C++创建DLL并用C#调用且同时实现对DLL的调试](https://blog.csdn.net/songyi160/article/details/51075023)

首先,您需要了解什么是**托管**,什么是**非托管**。一般可以认为：非托管代码主要是基于win 32平台开发的DLL，activeX的组件，托管代码是基于.net平台开发的。如果您想深入了解托管与非托管的关系与区别，及它们的运行机制，请您自行查找资料，本文件在此不作讨论。

 **调用DLL中的非托管函数一般方法**

**一**，**创建C#控制台应用程序：用于调用C++编写的DLL**

**二：用C++创建DLL**

添加头文件

.cpp引用.h

写头文件：

```c++
extern "C" _declspec(dllexport) int __stdcall test01(int a, int b);
```

写源文件：

```c++
int __stdcall test01(int a, int b)
{
    return a+b;
}
```

创建def模块，写入：

```c++
EXPORTS

test01 @ 1
```

先将解决方案切换到Release模式，再在CreateDLL项目名称上右击选择【生成】或【重新生成】

 在解决方案所在的目录中打开Release文件夹即可看到生成的DLL

**三：用C#项目调用C++创建DLL**

将C#项目设置为启动项目，并且将解决方案设置为Debug模式

应该在C#语言源程序中声明外部方法，其基本形式是：

```c#
[DLLImport(“DLL文件”)]
修饰符 extern 返回变量类型 方法名称 （参数列表）
```

**其中**：

DLL文件：包含定义外部方法的库文件。

修饰符： 访问修饰符，除了abstract以外在声明方法时可以使用的修饰符。

返回变量类型：在DLL文件中你需调用方法的返回变量类型。

方法名称：在DLL文件中你需调用方法的名称。

参数列表：在DLL文件中你需调用方法的列表。

**注意**：需要在程序声明中使用`System.Runtime.InteropServices`命名空间。

​      DllImport只能放置在方法声明上。

DLL文件必须位于程序当前目录或系统定义的查询路径中（即：系统环境变量中Path所设置的路径）。

返回变量类型、方法名称、参数列表一定要与DLL文件中的定义相一致。

 若要使用其它函数名，可以使用EntryPoint属性设置，如：

```c#
[DllImport("user32.dll", EntryPoint="MessageBoxA")]
static extern int MsgBox(int hWnd, string msg, string caption, int type);
```

其它可选的 DllImportAttribute 属性：

CharSet 指示用在入口点中的字符集，如：`CharSet=CharSet.Ansi`；

SetLastError 指示方法是否保留 Win32"上一错误"，如：`SetLastError=true`；

ExactSpelling 指示 EntryPoint 是否必须与指示的入口点的拼写完全匹配，如：`ExactSpelling=false`；

PreserveSig指示方法的签名应当被保留还是被转换， 如：`PreserveSig=true`；

CallingConvention指示入口点的调用约定， 如：`CallingConvention=CallingConvention.Winapi`；



待续。。。
