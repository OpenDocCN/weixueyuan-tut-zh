# Qt foreach 关键字用法（无师自通）

Qt 提供一个关键字 foreach (实际是 <QtGlobal> 里定义的一个宏）用于方便地访问容器里所有数据项。

foreach 关键字用于遍历容路中所有的项，使用 foreach 的句法是：

foreach (variable, container)

使用 foreach 的代码比使用迭代器更简洁。例如，使用 foreach 遍历一个 `QLinkedList<QString>` 的示例代码如下：

```
QLinkedList<QString> list;
...
QString str;
foreach (str, list)
    qDebug() << str;
```

用于迭代的变量也可以在 foreach 语句里定义，foreach 语句也可以使用花括号，可以使用 break 退出迭代，示例代码如下：

```
QLinkedList<QString> list;
...
foreach (const QString &str, list) {
    if (str.isEmpty())
        break;
    qDebug() << str;
}
```

对于 QMap 和 QHash，foreach 会自动访问“键-值”对里的值，所以无需调用 values()。如果需要访问键则可以调用 keys()，示例代码如下：

```
QMap<QString, int> map;
...
foreach (const QString &str, map.keys())
    qDebug() << str << ':' << map.value(str);
```

对于多值映射，可以使用两重 foreach 语句，示例代码如下:

```
QMultiMap<QString, int> map;
...
foreach (const QString &str, map.uniqueKeys()) {
    foreach (int i, map.values(str))
        qDebug() << str << ':' << i;
}
```

注意，foreach 关徤字遍历一个容器变量是创建了容器的一个副本，所以不能修改原来容器变量的数据项。