# QT 5学习之路

> 借鉴[《QT 学习之路2》](https://www.devbean.net/2012/08/qt-study-road-2-catelog/)

[TOC]



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

首先看`Newspaper`这个类。这个类继承了`QObject`类。只有继承了`QObject`类的类，才具有信号槽的能力。所以，为了使用信号槽，必须继承`QObject`。凡是`QObject`类（不管是直接子类还是间接子类），都应该在第一行代码写上`Q_OBJECT`。**不管是不是使用信号槽，都应该添加这个宏。**

**注意：**

- 这个宏将由 moc（我们会在后面章节中介绍 moc。这里你可以将其理解为一种预处理器，是比 C++ 预处理器更早执行的预处理器。） 做特殊处理，不仅仅是宏展开这么简单。moc 会读取标记了 Q_OBJECT 的**头文件**，生成以 moc_ 为前缀的文件，比如 newspaper.h 将生成 moc_newspaper.cpp。你可以到构建目录查看这个文件，看看到底增加了什么内容。
- 由于 moc 只处理头文件中的标记了`Q_OBJECT`的类声明，不会处理 cpp 文件中的类似声明。因此，如果我们的`Newspaper`和`Reader`类位于 main.cpp 中，是无法得到 moc 的处理的。如果想不使用头文件，而将`Newspaper`和`Reader`当做类写进main.cpp，则需要手动调用 moc 工具处理 main.cpp，并且将 main.cpp 中的`#include "newspaper.h"`改为`#include "moc_newspaper.h"`就可以了。不过一般**推荐使用头文件**。

`Newspaper`类的 public 和 private 代码块都比较简单，只不过它新加了一个 signals。signals 块所列出的，就是**该类的信号**。信号就是一个个的函数名，返回值是 void（因为无法获得信号的返回值，所以也就无需返回任何值），参数是该类需要让外界知道的数据。信号作为函数名，不需要在 cpp 函数中添加任何实现*（我们曾经说过，Qt 程序能够使用普通的 make 进行编译。没有实现的函数名怎么会通过编译？原因还是在 moc，moc 会帮我们实现信号函数所需要的函数体，所以说，moc 并不是单纯的将 Q_OBJECT 展开，而是做了很多额外的操作）*。

`Reader`类更简单。因为这个类需要接受信号，所以**我们将其继承了`QObject`**，并且添加了`Q_OBJECT`宏。后面则是默认构造函数和一个普通的成员函数。Qt 5 中，任何成员函数、static 函数、全局函数和 Lambda 表达式都可以作为槽函数。与信号函数不同，槽函数必须自己完成实现代码。槽函数就是普通的成员函数，因此作为成员函数，也会受到 public、private 等访问控制符的影响。*（我们没有说信号也会受此影响，事实上，如果信号是 private 的，这个信号就不能在类的外面连接，也就没有任何意义。）*

`main()`函数中，我们首先创建了`Newspaper`和`Reader`两个对象，然后使用`QObject::connect()`函数。这个函数我们上一节已经详细介绍过，这里应该能够看出这个连接的含义。然后我们调用`Newspaper`的`send()`函数。这个函数只有一个语句：发出信号。由于我们的连接，当这个信号发出时，自动调用 reader 的槽函数，打印出语句。

下面总结一下自定义信号槽需要注意的事项：

- 发送者和接收者都需要是`QObject`的子类（当然，槽函数是全局函数、Lambda 表达式等无需接收者的时候除外）；
- 使用 signals 标记信号函数，信号是一个函数声明，返回 void，不需要实现函数代码；
- 槽函数是普通的成员函数，作为成员函数，会受到 public、private、protected 的影响；
- 使用 **emit** 在恰当的位置发送信号；
- 使用`QObject::connect()`函数连接信号和槽。

------

### Qt 模块简介

Qt 5 模块分为 `Essentials Modules` 和` Add-on Modules` 两部分。前者是基础模块，在所有平台上都可用；后者是扩展模块，建立在基础模块的基础之上，在能够运行 Qt 的平台之上可以酌情引入。

Qt 基础模块分为以下几个：

- **Qt Core**，提供核心的非 GUI 功能，所有模块都需要这个模块。这个模块的类包括了动画框架、定时器、各个容器类、时间日期类、事件、IO、JSON、插件机制、智能指针、图形（矩形、路径等）、线程、XML 等。所有这些类都可以通过 <QtCore> 头文件引入。
- **Qt Gui**，提供 GUI 程序的基本功能，包括与窗口系统的集成、事件处理、OpenGL 和 OpenGL ES 集成、2D 图像、字体、拖放等。这些类一般由 Qt 用户界面类内部使用，当然也可以用于访问底层的 OpenGL ES 图像 API。Qt Gui 模块提供的是所有图形用户界面程序都需要的通用功能。
- **Qt Multimedia**，提供视频、音频、收音机以及摄像头等功能。这些类可以通过 <QtMultimedia> 引入，而且需要在 pro 文件中添加 QT += multimedia。
- **Qt Network**，提供跨平台的网络功能。这些类可以通过 <QtNetwork> 引入，而且需要在 pro 文件中添加 QT += network。
- **Qt Qml**，提供供 QML（一种脚本语言，也提供 JavaScript 的交互机制） 使用的 C++ API。这些类可以通过 <QtQml> 引入，而且需要在 pro 文件中添加 QT += qml。
- **Qt Quick**，允许在 Qt/C++ 程序中嵌入 Qt Quick（一种基于 Qt 的高度动画的用户界面，适合于移动平台开发）。这些类可以通过 <QtQuick> 引入，而且需要在 pro 文件中添加 QT += quick。
- **Qt SQL**，允许使用 SQL 访问数据库。这些类可以通过 <QtSql> 引入，而且需要在 pro 文件中添加 QT += sql。
- **Qt Test**，提供 Qt 程序的单元测试功能。这些类可以通过 <QtTest> 引入，而且需要在 pro 文件中添加 QT += testlib。
- **Qt Webkit**，基于 WebKit2 的实现以及一套全新的 QML API（顺便说一下，Qt 4.8 附带的是 QtWebkit 2.2）。

Qt 扩展模块则有更多的选择：

- **Qt 3D**，提供声明式语法，在 Qt 程序中可以简单地嵌入 3D 图像。Qt 3D 为 Qt Quick 添加了 3D 内容渲染。Qt 3D 提供了 QML 和 C++ 两套 API，用于开发 3D 程序。
- **Qt Bluetooth**，提供用于访问蓝牙无线设备的 C++ 和 QML API。
- **Qt Contacts**，用于访问地址簿或者联系人数据库的 C++ 和 QML API。
- **Qt Concurrent**，封装了底层线程技术的类库，方便开发多线程程序。
- **Qt D-Bus**，这是一个仅供 Unix 平台使用的类库，用于利用 D-Bus 协议进行进程间交互。
- **Qt Graphical Effects**，提供一系列用于实现图像特效的类，比如模糊、锐化等。
- **Qt Image Formats**，支持图片格式的一系列插件，包括 TIFF、MNG、TGA 和 WBMP。
- **Qt JS Backend**，该模块没有公开的 API，是 V8 JavaScript 引擎的一个移植。这个模块仅供 QtQml 模块内部使用。
- **Qt Location**，提供定位机制、地图和导航技术、位置搜索等功能的 QML 和 C++ API。
- **Qt OpenGL**，方便在 Qt 应用程序中使用 OpenGL。该模块仅仅为了程序从 Qt 4 移植到 Qt 5 的方便才保留下来，如果你需要在新的 Qt 5 程序中使用 OpenGL 相关技术，需要使用的是 QtGui 模块中的 QOpenGL。
- **Qt Organizer**，使用 QML 和 C++ API 访问组织事件（organizer event）。organizer API 是 Personal Information Management API 的一部分，用于访问 Calendar 信息。通过 Organizer API 可以实现：从日历数据库访问日历时间、导入 iCalendar 事件或者将自己的事件导出到 iCalendar。
- **Qt Print Support**，提供对打印功能的支持。
- **Qt Publish and Subscribe**，为应用程序提供对项目值的读取、导航、订阅等的功能。
- **Qt Quick 1**，从 Qt 4 移植过来的 QtDeclarative 模块，用于提供与 Qt 4 的兼容。如果你需要开发新的程序，需要使用 QtQuick 模块。
- **Qt Script**，提供脚本化机制。这也是为提供与 Qt 4 的兼容性，如果要使用脚本化支持，请使用 QtQml 模块的 QJS* 类。
- **Qt Script Tools**，为使用了 Qt Script 模块的应用程序提供的额外的组件。
- **Qt Sensors**，提供访问各类传感器的 QML 和 C++ 接口。
- **Qt Service Framework**，提供客户端发现其他设备的服务。Qt Service Framework 为在不同平台上发现、实现和访问服务定义了一套统一的机制。
- **Qt SVG**，提供渲染和创建 SVG 文件的功能。
- **Qt System Info**，提供一套 API，用于发现系统相关的信息，比如电池使用量、锁屏、硬件特性等。
- **Qt Tools**，提供了 Qt 开发的方便工具，包括 Qt CLucene、Qt Designer、Qt Help 以及 Qt UI Tools 。
- **Qt Versit**，提供了对 Versit API 的支持。Versit API 是 Personal Information Management API 的一部分，用于 QContacts 和 vCard 以及 QOrganizerItems 和 iCalendar 之间的相互转换。
- **Qt Wayland**，仅用于 Linux 平台，用于替代 QWS，包括 Qt Compositor API（server）和 Wayland 平台插件（clients）。
- **Qt WebKit**，从 Qt 4 移植来的基于 WebKit1 和 QWidget 的 API。
- **Qt Widgets**，使用 C++ 扩展的 Qt Gui 模块，提供了一些界面组件，比如按钮、单选框等。
- **Qt XML**，SAX 和 DOM 的 C++ 实现。该模块已经废除，请使用 QXmlStreamReader/Writer。
- **Qt XML Patterns**，提供对 XPath、XQuery、XSLT 和 XML Schema 验证的支持。
------

### MainWindow 简介

`QMainWindow`是 Qt 框架带来的一个预定义好的主窗口类。所谓主窗口，就是一个普通意义上的应用程序（不是指游戏之类的那种）最顶层的窗口。通常是由一个标题栏，一个菜单栏，若干工具栏和一个任务栏。

我们仔细看看这个窗口。虽然不太明显，但它实际上分成了几个部分：

![](E:\科研资料\To_be_a_Roboticist\mw-struct.png)

主窗口的最上面是 Window Title，也就是标题栏，通常用于显示标题和控制按钮，比如最大化、最小化和关闭等。通常，各个图形界面框架都会使用操作系统本地代码来生成一个窗口。所以，你会看到在 KDE 上面，主窗口的标题栏是 KDE 样式的；在 Windows 平台上，标题栏是 Windows 风格的。如果你不喜欢本地样式，比如 QQ 这种，它其实是自己将标题栏绘制出来，这种技术称为 DirectUI，也就是无句柄绘制，这不在本文的讨论范畴。Window Title 下面是 Menu Bar，也就是菜单栏，用于显示菜单。窗口最底部是 Status Bar，称为状态栏。当我们鼠标滑过某些组件时，可以在状态栏显示某些信息，比如浏览器中，鼠标滑过带有链接的文字，你会在底部看到链接的实际 URL。

除去上面说的三个横向的栏，中间是以矩形区域表示。我们可以看出，最外层称为 Tool Bar Area，用于显示工具条区域。之所以是矩形表示，是因为，Qt 的主窗口支持多个工具条。你可以将工具条拖放到不同的位置，因此这里说是 Area。我们可以把几个工具条并排显示在这里，就像 Word2003 一样，也可以将其分别放置，类似 Photoshop。在工具条区域内部是 Dock Widget Area，这是停靠窗口的显示区域。所谓停靠窗口，就像 Photoshop 的工具箱一样，可以停靠在主窗口的四周，也可以浮动显示。主窗口最中间称为 Central Widget，就是我们程序的工作区。通常我们会将程序最主要的工作区域放置在这里，类似 Word 的稿纸或者 Photoshop 的画布等等。

------

### 添加动作

**Qt 使用`QAction`类作为动作。**顾名思义，这个类就是代表了窗口的一个“动作”，这个动作可能显示在菜单，作为一个菜单项，当用户点击该菜单项，对用户的点击做出响应；也可能在工具栏，作为一个工具栏按钮，用户点击这个按钮就可以执行相应的操作。有一点值得注意：无论是出现在菜单栏还是工具栏，用户选择之后，所执行的动作应该都是一样的。因此，Qt 并没有专门的菜单项类，只是使用一个`QAction`类，抽象出公共的动作。当我们把`QAction`对象添加到菜单，就显示成一个菜单项，添加到工具栏，就显示成一个工具按钮。用户可以通过点击菜单项、点击工具栏按钮、点击快捷键来激活这个动作。

下面我们来看看如何在`QMainWindow`中使用`QAction`：

```C++
// ========== mainwindow.h
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>

class MainWindow : public QMainWindow
{
    Q_OBJECT
public:
    MainWindow(QWidget *parent = 0);
    ~MainWindow();

private:
    void open();

    QAction *openAction;
};

#endif // MAINWINDOW_H

// ========== mainwindow.cpp
#include <QAction>
#include <QMenuBar>
#include <QMessageBox>
#include <QStatusBar>
#include <QToolBar>

#include "mainwindow.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent)
{
    setWindowTitle(tr("Main Window"));

    openAction = new QAction(QIcon(":/images/doc-open"), tr("&Open..."), this);
    openAction->setShortcuts(QKeySequence::Open);
    openAction->setStatusTip(tr("Open an existing file"));
    connect(openAction, &QAction::triggered, this, &MainWindow::open);

    QMenu *file = menuBar()->addMenu(tr("&File"));
    file->addAction(openAction);

    QToolBar *toolBar = addToolBar(tr("&File"));
    toolBar->addAction(openAction);

    statusBar() ;
}

MainWindow::~MainWindow()
{
}

void MainWindow::open()
{
    QMessageBox::information(this, tr("Information"), tr("Open"));
}
```

上面的代码分别属于两个文件：mainwindow.h 和 mainwindow.cpp。为了让 MainWindow 运行起来，我们还需要修改 main() 函数如下：

```c++
int main(int argc, char *argv[])
{
    QApplication app(argc, argv);

    MainWindow win;
    win.show();

    return app.exec();
}
```

1. 首先，我们在`MainWindow`类中添加了一个私有函数`open()`和一个私有变量`openAction`。
2. 在 MainWindow 的构造函数中，第一行我们调用了`setWindowTitle()`，设置主窗口的标题。
3. 注意我们的文本使用`tr()`函数，这是一个用于 Qt 国际化的函数。在后续章节中我们可以看到，我们可以使用 Qt 提供的国际化工具，将`tr()`函数的字符串提取出来，进行国际化。由于所需进行国际化的文本应该被大多数人认识，所以，`tr()`函数里面一般会是英文文本。
4. 然后，我们在堆上创建了`openAction`对象。在`QAction`构造函数，我们传入了一个图标、一个文本和 this 指针。我们将在后面的文章中解释 this 指针的含义。
5. 图标我们使用了`QIcon`，传入值是一个字符串，这个字符串对应于 Qt 资源文件中的一段路径。`QIcon`的参数，以 : 开始，意味着从资源文件中查找资源。`:/images/doc-open`就是找到了这里的 document-open.png 这个文件。
6. `QAction`第二个参数中，文本值前面有一个 &，意味着这将成为一个快捷键。注意看截图中 File 的 F 有一个下划线。
7. `setShortcut()`函数，用于说明这个`QAction`的快捷键。Qt 的`QKeySequence`为我们定义了很多内置的快捷键，比如我们使用的 Open。你可以通过查阅 API 文档获得所有的快捷键列表。 这个与我们自己定义的有什么区别呢？简单来说，我们完全可以自己定义一个`tr("Ctrl+O")`来实现快捷键。原因在于，这是 Qt 跨平台性的体现。比如 PC 键盘和 Mac 键盘是不一样的，一些键在 PC 键盘上有，而 Mac 键盘上可能并不存在，或者反之。使用`QKeySequence`类来添加快捷键，会根据平台的不同来定义相应的快捷键。
8. `setStatusTip()`则实现了当用户鼠标滑过这个 action 时，会在主窗口下方的状态栏显示相应的提示。
9. `connect()`函数，将这个`QAction`的`triggered()`信号与`MainWindow`类的`open()`函数连接起来。当用户点击了这个`QAction`时，会自动触发`MainWindow`的`open()`函数。
10. 下面的`menuBar()`、`toolBar()`和`statusBar()`三个是`QMainWindow`的函数，用于创建并返回菜单栏、工具栏和状态栏。我们可以从代码清楚地看出，我们向菜单栏添加了一个 File 菜单，并且把这个`QAction`对象添加到这个菜单；同时新增加了一个 File 工具栏，也把`QAction`对象添加到了这个工具栏。我们可以看到，在菜单中，这个对象被显示成一个菜单项，在工具栏变成了一个按钮。至于状态栏，则是出现在窗口最下方，用于显示动作对象的提示信息的。
11. 

