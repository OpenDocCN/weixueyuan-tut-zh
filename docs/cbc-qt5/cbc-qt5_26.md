# Qt QPlainTextEdit 用法详解

QPlainTextEdit 是一个多行文本编辑器，用于显示和编辑多行简单文本。另外，还有一个 QTextEdit 组件，是一个所见即所得的可以编辑带格式文本的组件，以 HTML 格式标记符定义文本格式。

实例 samp4_6 演示 QPlainTextEdit（和 QComboBox）的使用，其运行时界面如图 1 所示。
![实例运行界面](img/3eeb53f540d5dc16eb5ebe1639125bf0.jpg)
图 1 实例运行界面
从《QComboBox》一节中的代码实现己经看出，使用 QPlainTextEdit::appendPlainText(const QString 函数就可以向 PlainTextEdit 组件添加一行字符串。

QPlainTextEdit 提供 cut()、copy()、paste()、undo()、redo()、clear()、selectAll() 等标准编辑功能的槽函数，QPlainTextEdit 还提供一个标准的右键快捷菜单。

## 逐行读取文字内容

如果要将 QPlainTextEdit 组件里显示的所有文字读取出来，有一个简单的函数 toPlainText() 可以将全部文字内容输出为一个字符串，其定义如下：

QString QPlainTextEdit::toPlainText() const

但是如果要逐行读取 QPlainTextEdit 组件里的字符串，则稍微麻烦一点。

下面是图 1 窗口中“文本框内容添加到 ComboBox”按钮的响应代码，它将 plainTextEdit 里的每一行作为一个项添加到 comboBox 里：

```
void Widget::on_btnToComboBox_clicked()
{
    //plainTextEdit 的内容逐行添加为 comboBox 的项
    QTextDocument* doc=ui->plainTextEdit->document () ; //文本对象
    int cnt=doc->blockCount () ;//回车符是一个 block
    QIcon icon(M:/images/icons/aim.ico");
    ui->comboBox->clear();
    for (int i=0; i<cnt;i++)
    {
        QTextBlock textLine=doc->findBlockByNumber (i) ; // 文本中的一段
        QString str=textLine.text();
        ui->comboBox->addItem(icon,str);
    }
}
```

QPlainTextEdit 的文字内容以 QTextDocument 类型存储，函数 document() 返回这个文档对象的指针。

QTextDocument 是内存中的文本对象，以文本块的方式存储，一个文本块就是一个段落，每个段落以回车符结束。QTextDocument 提供一些函数实现对文本内容的存取：

*   int blockCount()：获得文本块个数。
*   QTextBlock fmdBlockByNumber(int blockNumber)：读取某一个文本块，序号从 0 开始，至 blockCount()-1 结束。

一个 document 有多个 TextBlock，从 document 中读取出的一个文本块类型为 QTextBlock，通过 `QTextBlock::text()` 函数可以获取其纯文本文字。

## 使用 QPlainTextEdit 自带的快捷菜单

QPlainTextEdit 是一个多行文字编辑框，有自带的右键快捷菜单，可实现常见的编辑功能。在 UI 设计器里，选择为 plainTextEdit 的 `customContextMenuRequested()` 信号生成槽函数，编写如下的代码，就可以创建并显示 QPlainTextEdit 的标准快捷菜单：

```
void Widget::on_plainTextEdit_customContextMenuRequested(const QPoint &pos)
{
    //创建并显示标准弹出式菜单
    QMenu* menu=ui->plainTextEdit->createStandardContextMenu();
    menu->exec(pos);
}
```