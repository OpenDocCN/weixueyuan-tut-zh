# Java 包（package）：Java 系统包和自定义包

在编写 Java 程序时，随着程序架构越来越大，类的个数也越来越多，这时就会发现管理程序中维护类名称也是一件很麻烦的事，尤其是一些同名问题的发生。有时，开发人员还可能需要将处理同一方面的问题的类放在同一个目录下，以便于管理。Java 为了解决上述问题，提供了包机制。

## 系统包

包允许将类组合成较小的单元（类似文件夹），它基本上隐藏了类，并避免了名称上的冲突。包允许在更广泛的范围内保护类、数据和方法，可以在包内定义类，而在包外的代码不能访问该类。

包的 3 个作用如下：

1.  区分相同名称的类。
2.  能够较好地管理大量的类。
3.  控制访问范围。

在 Java 语言中，开发人员可以自定义包，也可以使用系统包，常用的系统包如表 1 所示。

表 1 Java 中常用的系统包

| 包 | 说明 |
| java.lang | Java 的核心类库，包含运行 Java 程序必不可少的系统类，如基本数据类型、基本数学函数、 字符串处理、异常处理和线程类等，系统默认加载这个包 |
| java.io | Java 语言的标准输入/输出类库，如基本输入/输出流、文件输入/输出、过滤输入/输出流等 |
| java.util | 包含如处理时间的 Date 类，处理动态数组的 Vector 类，以及 Stack 和 HashTable 类 |
| java.awt | 构建图形用户界面（GUI）的类库，低级绘图操作 Graphics 类、图形界面组件和布局管理 （如 Checkbox 类、Container 类、LayoutManger 接口等），以及用 户界面交互控制和事
件响应（如 Event 类） |
| java.awt.image | 处理和操纵来自网上的图片的 java 工具类库 |
| java.wat.peer | 很少在程序中直接用到，使得同一个 Java 程序在不同的软硬件平台上运行 |
| java.net | 实现网络功能的类库有 Socket 类、ServerSocket 类 |
| java.lang.reflect | 提供用于反射对象的工具 |
| java.util.zip | 实现文件压缩功能 |
| java.awt.datatransfer | 处理数据传输的工具类，包括剪贴板、字符串发送器等 |
| java.sql | 实现 JDBC 的类库 |
| java.rmi | 提供远程连接与载入的支持 |
| java. security | 提供安全性方面的有关支持 |

## 自定义包

Java 的系统包无须定义可以直接调用，当然也可以自定义包。下面来介绍自定义包的声明和使用方法。

#### 1\. 包的声明

为了声明一个包，首先必须建立一个相应的目录结构，子目录与包名一致，然后在需要放入该包的类文件开头声明包，形式如下：

```
package 包名;
```

不同程序文件内的类也可以同属于一个包，只要在这些程序文件前都加上同一个包的说明即可。

#### 2\. 包的使用

在 Java 中，为了装载使用已经编译好的包，通常可以使用以下三种方法。

1) 一是在要引用的类名前带上包名作为修饰符。代码如下：

```
bookexample.Test test = new bookexampie.Test();
```

其中，bookexample 是包名，Test 是包中的类名，test 是类的对象。

2) 二是在文件开头使用 import 引用包中的类。代码如下：

```
import bookexample.Test;
```

3) 三是在文件前使用 import 引用整个包。如下代码将 bookexample 整个包引入：

```
import bookexample.*;
```

在使用包时，可以用点（.）表示包所在的层次结构，用“.*”表示该目录下的所有内容。

提示：使用包时需要注意系统 classpath 路径的设置情况，它需要将包名对应目录的父目录包含在 classpath 路径中，否则编译时会出错，提示用户编译器找不到指定的类。