# Qt 顺序容器类和关联容器类详解

Qt 提供了多个基于模板的容器类，这些容器类可以用于存储指定类型的数据项，例如常用的字符串列表类 QStringList 就是从容器类 QLiSt<QString> 继承的，实现对字符串列表的添加、存储、删除等操作。

Qt 的容器类比标准模板库（STL）中的容器类更轻巧、安全和易于使用。这些容器类是隐式共享和可重入的，而且它们进行了速度和存储优化，因此可以减少可执行文件的大小。此外，它们还是线程安全的，也就是说它们作为只读容器时可被多个线程访问。

容器类是基于模板的类，如常用的容器类 QList<T>，T 是一个具体的类型，可以是 int、float 等简单类型，也可以是 Qstring、QDate 等类，但不能是 QObject 或任何其子类。T 必须是一个可赋值的类型，即 T 必须提供一个缺省的构造函数，一个可复制构造函数和一个赋值运算符。

例如用 QList<T> 定义一个字符串列表的容器，其定义方法是：

QList<QString> aList;

这样定义了一个 QList 容器类的变量 aList，它的数据项是 QString，所以 aList 可以用于处理字符串列表，例如：

aList.append("Monday");
aList.append("Tuesday");
aList.append("Wednesday");
QString str=aList[0];

Qt 的容器类分为顺序容器和关联容器。

## 顺序容器类

Qt 的顺序容器类有 QList、QLinkedList、QVector、QStack 和 QQueue。

#### QList

QList 是最常用的容器类，虽然它是以数组列表的形式实现的，但是在其前或后添加数据非常快，QList 以下标索引的方式对数据项进行访问。

QList 用于添加、插入、替换、移动、删除数据项的函数有：insert()、replace()、removeAt()、move()、swap()、append()、prepend()、removeFirst() 和 removeLast() 等。

QList 提供下标索引方式访问数据项，如同数组一样，也提供 at() 函数，例如：

QList<QString> list;
list << "one" << "two" << "three";
QString str1=list[1]; //str1=="two"
QString str0=list.at(0); //str0=="one"

QList 的 isEmpty() 函数在数据项为空时返回 true，size() 函数返回数据项的个数。

QList 是 Qt 中最常用的容器类，很多函数的参数传递都是采用 QList 容器类，例如 QAudioDeviceInfo 的静态函数 availableDevices() 的函数原型是：

QList<QAudioDeviceInfo> QAudioDeviceInfo::availableDevices(QAudio::Mode mode)

其返回数据就是 QAudioDeviceInfo 类型的 QList 列表。

#### QLinkedList

QLinkedList<T> 是链式列表，数据项不是用连续的内存存储的，它基于迭代器访问数据项，并且插入和删除数据项的操作时间相同。

除了不提供基于下标索引的数据项访问外，QLinkedList 的其他接口函数与 QList 基本相同。

#### QVector

QVector<T> 提供动态数组的功能，以下标索引访问数据。

QVector 的函数接口与 QList 几乎完全相同，QVector<T> 的性能比 QList<T> 更高，因为 QVector<P> 的数据项是连续存储的。

#### QStack

QStack<T> 是提供类似于堆栈的后入先出（LIFO）操作的容器类，push() 和 pop() 是主要的接口函数。例如：

```
QStack<int> stack;
stack.push(10);
stack.push(20);
stack.push(30);
while (!stack.isEmpty())
    cout << stack.pop() << endl;
```

程序会依次输出 30, 20, 10。

#### QQueue

QQueue<T> 是提供类似于队列先入先出（FIFO）操作的容器类。enqueue() 和 dequeue() 是主要操作函数。例如：

```
QQueue<int> queue;
queue.enqueue (10);
queue.enqueue(20);
queue.enqueue (30);
while (!queue.isEmpty())
    cout << queue.dequeue() << endl;
```

程序会依次输出 10, 20，30。

## 关联容器类

Qt 还提供关联容器类 QMap、QMultiMap、QHash、QMultiHash 和 QSet。

QMultiMap 和 QMultiHash 支持一个键关联多个值，QHash 和 QMultiHash 类使用散列函数进行查找，查找速度更快。

#### QSet

QSet 是基于散列表的集合模板类，它存储数据的顺序是不定的，查找值的速度非常快。 QSet<T> 内部就是用 QHash 实现的。

定义 QSet<T> 容器和输入数据的实例代码如下：

QSet<QString> set;
set << "dog" << "cat" << "tiger";

测试一个值是否包含于这个集合，用 contains() 函数，示例如下：

if (!set.contains("cat"))
    ...

#### QMap

QMap<Key, T> 提供一个字典（关联数组)，一个键映射到一个值。QMap 存储数据是按照键的顺序，如果不在乎存储顺序，使用 QHash 会更快。

定义 QMap<QString，int> 类型变量和赋值的示例代码如下：

```
QMap<QString, int> map;
map["one"] = 1;
map["two"] = 2;
map["three "] = 3;
```

也可以使用 insert() 函数赋值，或 remove() 移除一个键值对，示例如下：

map.insert("four", 4);
map.remove("two");

要查找一个值，使用运算符“[]”或 value() 函数，示例如下：

int num1 = map["one"];
int num2 = map.value("two");

如果在映射表中没有找到指定的键，会返回一个缺省构造值，例如，如果值的类型是字符串，会返回一个空的字符串。

在使用 value() 函数查找键值时，还可以指定一个缺省的返回值，示例如下：

timeout = map.value("TIMEOUT",30);

这表示如果在 map 里找到键“TIMEOUT”，就返回关联的值，否则返回值为 30。

#### QMultiMap

QMultiMap 是 QMap 的子类，是用于处理多值映射的便利类。

多值映射就是一个键可以对应多个值。QMap 正常情况下不允许多值映射，除非使用 QMap::insertMulti() 添加键值对。

QMultiMap 是 QMap 的子类，所以 QMap 的大多数函数在 QMultiMap 都是可用的，但是有几个特殊的，QMultiMap::insert() 等效于 QMap::insertMulti() , QMultiMap::replace() 等效于 QMap::insert()。

QMultiMap 使用示例如下：

```
QMultiMap<QString, int> map1, map2, map3;
map1.insert("plenty", 100);
mapl.insert("plenty", 2000); // map1.size() == 2
map2.insert("plenty", 5000); // map2.size() == 1
map3 = map1 + map2; // map3.size() == 3
```

QMultiMap 不提供“[]”操作符，使用 value() 函数访问最新插入的键的单个值。如果要获取一个键对应的所有值，使用 values() 函数，返回值是 QList<T> 类型。

```
QList<int> values = map.values("plenty");
for (int i = 0; i < values.size(); ++i)
    cout << values.at(i) << endl;
```

#### QHash

QHash 是基于散列表来实现字典功能的模板类，QHash<Key，T> 存储的键值对具有非常快的查找速度。

QHash 与 QMap 的功能和用法相似，区别在于以下几点：

1.  QHash 比 QMap 的查找速度快；
2.  在 QMap 上遍历时，数据项是按照键排序的，而 QHash 的数据项是任意顺序的；
3.  QMap 的键必须提供“<”运算符，QHash 的键必须提供“==”运算符和一个名称为 qHash() 的全局散列函数。

#### QMultiHash

QMultiHash 是 QHash 的子类，是用于处理多值映射的便利类，其用法与 QMultiMap 类似。