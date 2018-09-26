# QT 5学习之路

> 借鉴[《QT 学习之路2》](https://www.devbean.net/2012/08/qt-study-road-2-catelog/)




## 基础教程

### 信号槽

**所谓信号槽，实际就是观察者模式。**当某个事件发生之后，比如，按钮检测到自己被点击了一下，它就会发出一个信号（signal）。这种发出是没有目的的，类似**广播**。如果有对象对这个信号感兴趣，它就会使用**连接（connect）函数**，意思是，用自己的一个函数（成为槽（slot））来处理这个信号。

```C++
#include <QApplication>
#include <QPushButton>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);

    QPushButton button("Quit");
    QObject::connect(&button, &QPushButton::clicked, &QApplication::quit);
    button.show();

    return app.exec();
}
```

我们这里要仔细分析`QObject::connect()`这个函数。

```c++
QMetaObject::Connection connect(const QObject *, const char *,
                                const QObject *, const char *,
                                Qt::ConnectionType);

QMetaObject::Connection connect(const QObject *, const QMetaMethod &,
                                const QObject *, const QMetaMethod &,
                                Qt::ConnectionType);

QMetaObject::Connection connect(const QObject *, const char *,
                                const char *,
                                Qt::ConnectionType) const;

QMetaObject::Connection connect(const QObject *, PointerToMemberFunction,
                                const QObject *, PointerToMemberFunction,
                                Qt::ConnectionType)

QMetaObject::Connection connect(const QObject *, PointerToMemberFunction,
                                Functor);
```

这五个重载的返回值都是`QMetaObject::Connection`，现在我们不去关心这个返回值。下面我们先来看看`connect()`函数最常用的一般形式：

```c++
connect(sender,   signal,
        receiver, slot);
```

这是我们最常用的形式。`connect()`一般会使用前面四个参数，第一个是**发出信号的对象**，第二个是**发送对象发出的信号**，第三个是**接收信号的对象**，第四个是**接收对象在接收到信号之后所需要调用的函数**。也就是说，当 sender 发出了 signal 信号之后，会自动调用 receiver 的 slot 函数。

由此我们可以看出，`connect()`函数，sender 和 receiver 没有什么区别，都是`QObject`指针；主要是 signal 和 slot 形式的区别。具体到我们的示例，我们的`connect()`函数显然是使用的第五个重载，最后一个参数是`QApplication`的 static 函数`quit()`。也就是说，当我们的 button 发出了`clicked()`信号时，会调用`QApplication`的`quit()`函数，使程序退出。（这里不需要receiver，因为是quit函数）

借助 Qt 5 的信号槽语法，我们可以将一个对象的信号连接到 Lambda 表达式，例如：

```c++
#include <QApplication>
#include <QPushButton>
#include <QDebug>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);

    QPushButton button("Quit");
    QObject::connect(&button, &QPushButton::clicked, [](bool) {
        qDebug() << "You clicked me!";
    });
    button.show();

    return app.exec();
}
```

注意这里的 Lambda 表达式接收一个 bool 参数（由clicked()传递的），这是因为`QPushButton`的`clicked()`信号实际上是有一个参数的。Lambda 表达式中的`qDebug()`类似于`cout`，将后面的字符串打印到标准输出。如果要编译上面的代码，你需要在 pro 文件中添加这么一句：

```c
QMAKE_CXXFLAGS += -std=c++0x  //貌似不需要，不知道什么用处
```

------

### 自定义信号槽

经典的观察者模式在讲解举例的时候通常会举报纸和订阅者的例子。有一个报纸类`Newspaper`，有一个订阅者类`Subscriber`。`Subscriber`可以订阅`Newspaper`。这样，当`Newspaper`有了新的内容的时候，`Subscriber`可以立即得到通知。在这个例子中，观察者是`Subscriber`，被观察者是`Newspaper`。在经典的实现代码中，观察者会将自身注册到被观察者的一个容器中（比如`subscriber.registerTo(newspaper)`）。被观察者发生了任何变化的时候，会主动遍历这个容器，依次通知各个观察者（`newspaper.notifyAllSubscribers()`）。

```c++
#include <QObject>

////////// newspaper.h
class Newspaper : public QObject
{
    Q_OBJECT     //必须添加
public:
    Newspaper(const QString & name) :
        m_name(name)
    {
    }

    void send()
    {
        emit newPaper(m_name);
    }

signals:
    void newPaper(const QString &name);

private:
    QString m_name;
};

////////// reader.h
#include <QObject>
#include <QDebug>

class Reader : public QObject
{
    Q_OBJECT
public:
    Reader() {}

    void receiveNewspaper(const QString & name)
    {
        qDebug() << "Receives Newspaper: " << name;
    }
};

////////// main.cpp
#include <QCoreApplication>

#include "newspaper.h"
#include "reader.h"

int main(int argc, char *argv[])
{
    QCoreApplication app(argc, argv);

    Newspaper newspaper("Newspaper A");
    Reader reader;
    QObject::connect(&newspaper, &Newspaper::newPaper,
                     &reader,    &Reader::receiveNewspaper);
    newspaper.send();

    return app.exec();
}
```

首先看`Newspaper`这个类。这个类继承了`QObject`类。只有继承了`QObject`类的类，才具有信号槽的能力。所以，为了使用信号槽，必须继承`QObject`。凡是`QObject`类（不管是直接子类还是间接子类），都应该在第一行代码写上`Q_OBJECT`。
