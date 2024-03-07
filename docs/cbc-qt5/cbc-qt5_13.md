# Qt 元对象和属性系统详解

Qt 是一个用标准 C++ 编写的跨平台开发类库，它对标准 C++ 进行了扩展，引入了元对象系统、信号与槽、属性等特性，使应用程序的开发变得更高效。

本节将介绍 Qt 的这些核心特点，对于理解和编写高效的 Qt C++ 程序是大有帮助的。

## Qt 的元对象系统

Qt 的元对象系统（Meta-Object System）提供了对象之间通信的信号与槽机制、运行时类型信息和动态属性系统。

元对象系统由以下三个基础组成：

1.  QObject 类是所有使用元对象系统的类的基类。
2.  在一个类的 private 部分声明 Q_OBJECT 宏，使得类可以使用元对象的特性，如动态属性、信号与槽。
3.  MOC（元对象编译器）为每个 QObject 的子类提供必要的代码来实现元对象系统的特性。

构建项目时，MOC 工具读取 C++ 源文件，当它发现类的定义里有 Q_OBJECT 宏时，它就会为这个类生成另外一个包含有元对象支持代码的 C++ 源文件，这个生成的源文件连同类的实现文件一起被编译和连接。

除了信号与槽机制外，元对象还提供如下一些功能：

*   QObject::metaObject() 函数返回类关联的元对象，元对象类 QMetaObject 包含了访问元对象的一些接口函数，例如 QMetaObject::className() 函数可在运行时返回类的名称字符串。

    QObject *obj = new QPushButton;
    obj->metaObject()->className (); //返回"QPushButton"

*   QMetaObject::newInstance() 函数创建类的一个新的实例。
*   QObject::inherits(const char *className) 函数判断一个对象实例是否是名称为 className 的类或 QObject 的子类的实例。例如：

    QTimer *timer = new QTimer; // QTimer 是 QObject 的子类
    timer->inherits ("QTimer"); // 返回 true
    timer->inherits ("QObject");  // 返回 true
    timer->inherits ("QAbstractButton");//返回 false,不是 QAbstractButton 的子类

*   QObject::tr() 和 QObject::trUtf8() 函数可翻译字符串，用于多语言界面设计，后续章会专门介绍多语言界面设计。
*   QObject::setProperty() 和 QObject::property() 函数用于通过属性名称动态设置和获取属性值。

对于 QObject 及其子类，还可以使用 qobject_cast() 函数进行动态投射（dynamic cast）。例如，假设 QMyWidget 是 QWidget 的子类并且在类定义中声明了 Q_OBJECT 宏。创建实例使用下面的语句：

QObject *obj = new QMyWidget;

变量 obj 定义为 QObject 指针，但它实际指向 QMyWidget 类，所以可以正确投射为 QWidget，即：

QWidget *widget = qobject_cast<QWidget *>(obj);

从 QObject 到 QWidget 的投射是成功的，因为 obj 实际是 QMyWidget 类，是 QWidget 的子类。也可以将其成功投射为 QMyWidget，即：

QMyWidget *myWidget = qobject_cast<QMyWidget *>(obj);

投射为 QMyWidget 是成功的，因为 qobject_cast() 并不区分 Qt 内建的类型和用户自定义类型。但是，若要将 obj 投射为 QLabel 则是失败的，即：

QLabel * label - qobject_cast<QLabel *>(obj);

这样投射是失败的，返回指针 label 为 NULL，因为 QMyWidget 不是 QLabel 的子类。

使用动态投射，使得程序可以在运行时对不同的对象做不同的处理。

## 属性系统

#### 属性定义

Qt 提供一个 Q_PROPERTY() 宏可以定义属性，它也是基于元对象系统实现的。Qt 的属性系统与 C++ 编译器无关，可以用任何标准的 C++ 编译器编译定义了属性的 Qt C++ 程序。

在 QObject 的子类中，用宏 Q_PROPERTY() 定义属性，其使用格式如下：

Q_PROPERTY(type name (READ getFunction [WRITE setFunction] | MEMBER meznberName [(READ getFunction | WRITE setFunction)])
    [RESET resetFunction]
    [NOTIFY notifySignal]
    [REVISION int]
    [DESIGNABLE bool]
    [SCRIPTABLE bool]
    [STORED bool]
    [USER bool]
    [CONSTANT]
    [FINAL])

Q_PROPERTY 宏定义一个返回值类型为 type，名称为 name 的属性，用 READ、WRITE 关键字定义属性的读取、写入函数，还有其他的一些关键字定义属性的一些操作特性。属性的类型可以是 QVariant 支持的任何类型，也可以用户自定义类型。

Q_PROPERTY 宏定义属性的一些主要关键字的意义如下：

*   READ 指定一个读取属性值的函数，没有 MEMBER 关键字时必须设置 READ。
*   WRITE 指定一个设定属性值的函数，只读属性没有 WRITE 设置。
*   MEMBER 指定一个成员变量与属性关联，成为可读可写的属性，无需再设置 READ 和 WRITE。
*   RESET 是可选的，用于指定一个设置属性缺省值的函数。
*   NOTIFY 是可选的，用于设置一个信号，当属性值变化时发射此信号。
*   DESIGNABLE 表示属性是否在 Qt Designer 里可见，缺省为 true。
*   CONSTANT 表示属性值是一个常数，对于一个对象实例，READ 指定的函数返回值是常数，但是每个实例的返回值可以不一样。具有 CONSTANT 关键字的属性不能有 WRITE 和 NOTIFY 关键字。
*   FINAL 表示所定义的属性不能被子类重载。

QWidget 类定义属性的一些例子如下：

Q_PROPERTY(bool focus READ hasFocus)
Q_PROPERTY(bool enabled READ isEnabled WRITE setEnabled)
Q_PROPERTY(QCursor cursor READ cursor WRITE setCursor RESET unsetCursor)

#### 属性的使用

不管是否用 READ 和 WRITE 定义了接口函数，只要知道属性名称，就可以通过 QObject::property() 读取属性值，并通过 QObject::setProperty() 设置属性值。例如：

QPushButton *button = new QPushButton;
QObject *object = button；
object->setProperty("flat", true);
bool isFlat= object->property ("flat");

#### 动态属性

QObject::setProperty() 函数可以在运行时为类定义一个新的属性，称之为动态属性。动态属性是针对类的实例定义的。

动态属性可以使用 QObject::property() 查询，就如在类定义里用 Q_PROPERTY 宏定义的属性一样。

例如，在数据表编辑界面上，一些字段是必填字段，就可以在初始化界面时为这些字段的关联显示组件定义一个新的 required 属性，并设置值为“true”，如：

editName->setProperty("required", "true");
comboSex->setProperty("required", "true");
checkAgree->setProperty("required", "true");

然后，可以应用下面的样式定义将这种必填字段的背景颜色设置为亮绿色。

*[required="true"]{background-color:lime}

#### 类的附加信息

属性系统还有一个宏 Q_CLASSINFO()，可以为类的元对象定义“名称——值”信息，如：

```
class QMyClass:public QObject {
    Q_OBJECT
    Q_CLASSINFO("author", "Wang")
    Q_CLASSINFO ("company", "UPC")
    Q_CLASSINFO("version ", "3.0.1")
  public:
    ...
};
```

用 Q_CLASSINFO() 宏定义附加类信息后，可以通过元对象的一些函数获取类的附加信息，如 classlnfo(int) 获取某个附加信息，函数原型定义如下：

QMetaClassInfo QMetaObject::classInfo(int index) const

返回值是 QMetaClassInfo 类型，有 name() 和 value() 两个函数，可获得类附加信息的名称和值。