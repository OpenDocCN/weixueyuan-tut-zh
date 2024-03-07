# Java File 类（文件操作类）详解

在操作文件之前必须创建一个指向文件的链接或者实例化一个文件对象，也可以指定一个不存在的文件从而创建它。

Java 中的 File 类是文件和目录路径名的抽象形式。使用 File 类可以获取文件本身的一些信息，例如文件所在的目录、文件长度、文件读写权限等。本节将对 File 类进行详细介绍。

## File 类简介

在 Java 中，File 类是 java.io 包中唯一代表磁盘文件本身的对象。File 类定义了一些与平台无关的方法来操作文件，File 类主要用来获取或处理与磁盘文件相关的信息，像文件名、 文件路径、访问权限和修改日期等，还可以浏览子目录层次结构。

File 类表示处理文件和文件系统的相关信息。也就是说，File 类不具有从文件读取信息和向文件写入信息的功能，它仅描述文件本身的属性。

File 类提供了如下三种形式构造方法。

1.  File(File parent,String child)：根据 parent 抽象路径名和 child 路径名字符串创建一个新 File 实例。
2.  File(String pathname)：通过将给定路径名字符串转换成抽象路径名来创建一个新 File 实例。如果给定字符串是空字符串，则结果是空的抽象路径名。
3.  File(String parent,String child)：根据 parent 路径名字符串和 child 路径名字符串创建一个新 File 实例。

使用任意一个构造方法都可以创建一个 File 对象，然后调用其提供的方法对文件进行操作。在表 1 中列出了 File 类的常用方法及说明。

表 1 File 类的常用方法

| 方法名称 | 说明 |
| boolean canRead() | 测试应用程序是否能从指定的文件中进行读取 |
| boolean canWrite() | 测试应用程序是否能写当前文件 |
| boolean delete() | 删除当前对象指定的文件 . |
| boolean exists() | 测试当前 File 是否存在 |
| String getAbsolutePath() | 返回由该对象表示的文件的绝对路径名 |
| String getName() | 返回表示当前对象的文件名 |
| String getParent() | 返回当前 File 对象路径名的父路径名，如果此名没有父路径则为 null |
| boolean isAbsolute() | 测试当前 File 对象表示的文件是否为一个绝对路径名 |
| boolean isDirectory() | 测试当前 File 对象表示的文件是否为一个路径 |
| boolean isFile() | 测试当前 File 对象表示的文件是否为一个“普通”文件 |
| long lastModified() | 返回当前 File 对象表示的文件最后修改的时间 |
| long length() | 返回当前 File 对象表示的文件长度 |
| String[] list() | 返回当前 File 对象指定的路径文件列表 |
| String[] list(FilenameFilter) | 返回当前 File 对象指定的目录中满足指定过滤器的文件列表 |
| boolean mkdir() | 创建一个目录，它的路径名由当前 File 对象指定 |
| boolean mkdirs() | 创建一个目录，它的路径名由当前 File 对象指定 |
| boolean renameTo(File) | 将当前 File 对象指定的文件更名为给定参数 File 指定的路径名 |

注意：假设在 Windows 操作系统中有一文件 `D:\javaspace\hello.java`，在 Java 中使用的时候，其路径的写法应该为 `D:/javaspace/hello.java` 或者 `D:\\javaspace\\hello.java`。

## 获取文件属性

在 Java 中获取文件属性信息的第一步是先创建一个 File 类对象并指向一个已存在的文件， 然后调用表 1 中的方法进行操作。

#### 例 1

假设有一个文件位于 C:\windows\notepad.exe。编写 Java 程序获取并显示该文件的长度、是否可写、最后修改日期以及文件路径等属性信息。实现代码如下：

```
package ch13;
import java.io.File;
import java.io.IOException;
import java.util.Date;
public class Test02
{
    public static void main(String[] args)
    {
         String path="C:/windows/";    //指定文件所在的目录
         File f=new File(path,"notepad.exe");    //建立 File 变量,并设定由 f 变量引用
         System.out.println("C:\\windows\\notepad.exe 文件信息如下：");
         System.out.println("============================================");
         System.out.println("文件长度："+f.length()+"字节");
         System.out.println("文件或者目录："+(f.isFile()?"是文件":"不是文件"));
         System.out.println("文件或者目录："+(f.isDirectory()?"是目录":"不是目录"));
         System.out.println("是否可读："+(f.canRead()?"可读取":"不可读取"));
         System.out.println("是否可写："+(f.canWrite()?"可写入":"不可写入"));
         System.out.println("是否隐藏："+(f.isHidden()?"是隐藏文件":"不是隐藏文件"));
         System.out.println("最后修改日期："+new Date(f.lastModified()));
         System.out.println("文件名称："+f.getName());
         System.out.println("文件路径："+f.getPath());
         System.out.println("绝对路径："+f.getAbsolutePath());
    }
}
```

在上述代码中 File 类构造方法的第一个参数指定文件所在位置，这里使用“C:/”作为文件的实际路径；第二个参数指定文件名称。创建的 File 类对象为 f，然后通过 f 调用方法获取相应的属性，最终运行效果如下所示。

```
C:\windows\notepad.exe 文件信息如下：
============================================
文件长度：193536 字节
文件或者目录：是文件
文件或者目录：不是目录
是否可读：可读取
是否可写：可写入
是否隐藏：不是隐藏文件
最后修改日期：Mon Dec 28 02:55:19 CST 2015
文件名称：notepad.exe
文件路径：C:\windows\notepad.exe
绝对路径：C:\windows\notepad.exe
```

## 创建和删除文件

File 类不仅可以获取已知文件的属性信息，还可以在指定路径创建文件，以及删除一个文件。创建文件需要调用 createNewFile() 方法，删除文件需要调用 delete() 方法。无论是创建还是删除文件通常都先调用 exists() 方法判断文件是否存在。

#### 例 2

假设在 C:/Config 目录下有一个 dbConfig.xml 文件是程序的配置文件，程序启动时会检测该文件是否存在，如果不存在则创建；如果存在则删除它再创建。

实现代码如下：

```
package ch13;
import java.io.File;
import java.io.IOException;
import java.util.Date;
public class Test03
{
    public static void main(String[] args) throws IOException
    {
          String path="C:/config/";    //指定文件目录
          String filename="dbConfig.xml";    //指定文件名称
          File f=new File(path,filename);    //创建指向文件的 File 对象
          if(f.exists())    //判断文件是否存在
          {
              f.delete();    //存在则先删除
           }
         f.createNewFile();    //再创建
    }
}
```

## 创建和删除目录

File 类除了对文件的创建和删除外，还可以创建和删除目录。创建目录需要调用 mkdir() 方法，删除目录需要调用 delete() 方法。无论是创建还是删除目录都可以调用 exists() 方法判断目录是否存在。

#### 例 3

编写一个程序判断 C 盘根目录下是否存在 config 目录，如果存在则先删除再创建。实现代码如下：

```
package ch13;
import java.io.File;
import java.io.IOException;
import java.util.Date;
public class Test04
{
    public static void main(String[] args)
    {
          String path="C:/config/";    //指定目录位置
          File f=new File(path);    //创建 File 对象
          if(f.exists())
          {
              f.delete();
          }
          f.mkdir();    //创建目录
    }
}
```

## 遍历目录

通过遍历目录可以在指定的目录中查找文件，或者显示所有的文件列表。File 类的 list() 方法提供了遍历目录功能，该方法有如下两种重载形式。

#### 1\. String[] list()

该方法表示返回由 File 对象表示目录中所有文件和子目录名称组成的字符串数组，如果调用的 File 对象不是目录，则返回 null。

提示：list() 方法返回的数组中仅包含文件名称，而不包含路径。但不保证所得数组中的相同字符串将以特定顺序出现，特别是不保证它们按字母顺序出现。

#### 2\. String[] list(FilenameFilter filter)

该方法的作用与 list() 方法相同，不同的是返回数组中仅包含符合 filter 过滤器的文件和目录，如果 filter 为 null，则接受所有名称。

#### 例 4

假设要遍历 C 盘根目录下的所有文件和目录，并显示文件或目录名称、类型及大小。使用 list() 方法的实现代码如下：

```
package ch13;
import java.io.File;
import java.io.IOException;
import java.util.Date;
public class Test05
{
    public static void main(String[] args)
    {
        File f=new File("C:/");    //建立 File 变量,并设定由 f 变量变数引用
        System.out.println("文件名称\t\t 文件类型\t\t 文件大小");
        System.out.println("===================================================");
        String fileList[]=f.list();    //调用不带参数的 list()方法
        for (int i=0;i<fileList.length;i++)
        {    //遍历返回的字符数组
            System.out.print(fileList[i]+"\t\t");
            System.out.print((new File("C:/",fileList[i])).isFile()?"文件"+ "\t\t":"文件夹"+"\t\t");
            System.out.println((new File("C:/",fileList[i])).length()+"字节");
        }
    }
}
```

由于 list() 方法返回的字符数组中仅包含文件名称，因此为了获取文件类型和大小，必须先转换为 File 对象再调用其方法。如下所示的是实例的运行效果：

```
文件名称        文件类型        文件大小
===================================================
$360Section        文件夹        12288 字节
$Recycle.Bin        文件夹        0 字节
360SANDBOX        文件夹        4096 字节
adcfg.json        文件        61 字节
AMD        文件夹        0 字节
AppData        文件夹        0 字节
Boot        文件夹        4096 字节
bootmgr        文件        383786 字节
Config.Msi        文件夹        786432 字节
dlcache        文件夹        0 字节
Documents and Settings        文件夹        0 字节
Drivers        文件夹        4096 字节
DTLService.exe        文件        143208 字节
DTLSvcCore.dll        文件        85504 字节
EFI        文件夹        0 字节
Fireworks8-chs.exe        文件        92826464 字节
Fireworks8-chs.zip        文件        92646389 字节
grldr        文件        171136 字节
InstallConfig.ini        文件        48 字节
Intel        文件夹        0 字节
KRECYCLE        文件夹        0 字节
offline_FtnInfo.txt        文件        296 字节
pagefile.sys        文件        8436592640 字节
PerfLogs        文件夹        0 字节
Program Files        文件夹        8192 字节
Program Files (x86)        文件夹        12288 字节
ProgramData        文件夹        8192 字节
Readme-说明.htm        文件        3062 字节
RECYCLER        文件夹        0 字节
System Volume Information        文件夹        4096 字节
syt        文件夹        0 字节
temp        文件夹        4096 字节
Users        文件夹        4096 字节
Windows        文件夹        28672 字节
```

#### 例 5

假设希望只列出目录下的某些文件，这就需要调用带过滤器参数的 list() 方法。首先需要创建文件过滤器，该过滤器必须实现 `java.io.FilenameFilter` 接口，并在 accept() 方法中指定允许的文件类型。

如下所示为允许 SYS、TXT 和 BAK 格式文件的过滤器实现代码：

```
import java.io.File;
import java.io.FilenameFilter;
public class ImageFilter implements FilenameFilter
{
    //实现 FilenameFilter 接口
    @Override
    public boolean accept(File dir,String name)
    {
        //指定允许的文件类型
        return name.endsWith(".sys")||name.endsWith(".txt")||name.endsWith(".bak");
    }
}
```

上述代码创建的过滤器名称为 ImageFilter，接下来只需要将该名称传递给 list() 方法即可实现筛选文件。如下所示为修改后的 list() 方法，其他代码与例 4 相同，这里不再重复。

```
String fileList[]=f.list(new ImageFilter());
```

再次运行程序，遍历结果如下所示：

```
文件名称        文件类型        文件大小
===================================================
offline_FtnInfo.txt        文件        296 字节
pagefile.sys        文件        8436592640 字节
```