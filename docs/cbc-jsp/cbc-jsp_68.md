# File 类（在 JSP 中的使用）详解

File 对象用来获取文件本身的一些信息，如文件所在的目录、文件的长度、文件读/写权限等，但 File 对象并不涉及对文件的读/写操作。

创建 File 对象的构造方法有三个：

1.  File(String filename)
2.  File(String directoryPath,String filename)
3.  File(File f,String filename)

其中，filename 是文件名字，directoryPath 是文件的路径，f 指定文件目录。

对于第一个构造方法，filename 是文件名字或文件的绝对路径，如 filename="Hello.txt" 或 filename="c:/mybook/A.txt"。

对于第二个构造方法，directoryPath 是文件的路径，filename 是文件名字，如 directoryPath="c:/mybook/"，filename="A.txt"。

对于第三个构造方法，参数 f 是指定一个目录，filename 是文件名字，如 f=new File("c:/mybook")，filename="A.txt"。

#### 提示：

使用 File(String filename) 创建文件时，该文件被认为是与当前应用程序在同一目录中，由于 JSP 引擎是在 bin 下启动执行的，所以该文件也在 bin 目录下，即 D:\apache-tomcat-6.0.13\bin。

## 获取文件的属性

用户可以使用 File 类的下列方法获取文件本身的一些信息。

*   public String getName()：获取文件的名字。
*   public boolean canRead()：判断文件是否是可读的。
*   public boolean canWrite()：判断文件是否可被写入。
*   public boolean exists()：判断文件是否存在。
*   public long length()：获取文件的长度（单位是字节）。
*   public String getAbsolutePath()：获取文件的绝对路径。
*   public String getParent()：获取文件的父目录。
*   public boolean isFile()：判断文件是否是一个正常文件，而不是目录。
*   public boolean isDirectroy()：判断文件是否是一个目录。
*   public boolean isHidden()：判断文件是否是隐藏文件。
*   public long lastModified()：获取文件最后修改的时间（时间是从 1970 年午夜（格林尼治时间）至文件最后修改时刻的毫秒数）。

【例 1】使用上述的一些方法，获取某些文件的信息。

```
<%@page language="java" contentType="text/html" pageEncoding="utf-8" %>
<%@page import="java.io.*"%>
<head>
<title>
    获取文件属性示例
</title>
</head>
<%
    File f1=new File("E:\documents\java\apache-tomcat-6.0.16\webapps\ROOT","build.xml");
    File f2=new File("java.sh");
%>
文件 build.xml 是可读的吗？
<%=f1.canRead()%>
<br/> 
文件 build.xml 的长度：
<%=f1.length()%>字节
<br/>
java.sh 是目录吗？
<%=f2.isDirectory()%>
<br/>
build.xml 的父目录是:
<%=f1.getParent()%>
<br/>
java.sh 的绝对路径是：
<%=f2.getAbsolutePath()%>
</font>
</body>
</html>
```

## 创建目录的基本操作

#### 1\. 创建目录

File 对象调用方法 public boolean mkdir() 创建一个目录，如果创建成功就返回 true，否则返回 false（如果该目录已经存在将返回 false）。

【例 2】在 ch09 目录下创建一个名字为 Students 的目录：

```
<%@page language="java" contentType="text/html" pageEncoding="utf-8" %>
<%@page import="java.io.*"%>
<head>
<title>
    创建目录
</title>
</head>
<%
    File dir=new File("E:java\apache-tomcat-6.0.16\webapps\ch09","Students");
%>
在 ch09 下创建一个新的目录：student,<br>成功创建了吗？
<%=dir.mkdir()%>
<br/>   
student 是目录吗？
<%=dir.isDirectory()%>
</font>
</body>
</html>
```

#### 2\. 列出目录中的文件

如果 File 对象是一个目录，那么该对象可以调用下述方法列出该目录下的文件和子目录。

*   public String[] list()：用字符串形式返回目录下的全部文件。
*   public File[] listFiles()：用 File 对象形式返回目录下的全部文件。

【例 3】输出 ch09 目录下的全部文件和子目录：

```
<%@page contentType="text/html;charset=utf-8" %>
<%@page import="java.io.*" %>
<html>
<body bgcolo=cyan>
<font Size=2>
<%
    File dir=new File("E:/apache-tomcat-6.0.13/webapps/ch09");
    File file[]=dir.listFiles();
%>
<br>目录有：
<%
    for(int i=0;i<file.length;i++)
    {
        if(file[i].isDirectory())
        out.print("<br>"+file[il.toString()];
    }
%>
<br>文件名字:
<%
    for( int i=0;i<file.length;i++)
    {
        if(file[i].isFile))
            out.print("<br>"+file[i].toString());
    }
%>
</font>
</body>
</html>
```

#### 3\. 列出指定类型的文件

有时需要列出目录下指定类型的文件，如 .jsp、.txt 等格式的文件，可以使用 File 类的以下两个方法。

*   public String[] list(FilenameFilter obj)：该方法用字符串形式返回目录下的指定类型的所有文件。
*   public File[]listFiles(FilenameFilter obj)：该方法用 File 对象返回目录下的指定类型 的所有文件。

【例 4】列出 ch09 目录下部分 JSP 文件的名字：

```
<%@page contentType="text/html;charset=utf-8" %>
<%@page import="java.io.*"%>
<html>
<body bgcolor=cyan>
<font Size=2>
<%!
    class FileJSP implements FilenameFilter
    {
        String str=null;
        FileJSP(String s)
        {
            str="."+s;
        }
        public boolean accept(File dir,String name)
        {
            return name.endsWith(str) ;
        }
    }
%>
<br> ch09 目录中的 jsp 文件：
<%
    File dir=new File("D:/apache-toracat-6.0.13/webapps/ch05");
    FileJSP file_jsp=new FileJSP("jsp");
    String file_name[]=dir.list(file_jsp);
    for(int i=0;i<file_name.length;i++)
        out.print("<br>"+file_name[i]);
%>
</font>
</body>
</html>
```

## 删除文件和目录

File 对象调用方法 public boolean delete() 可以删除当前对象代表的文件或目录。如果 File 对象表示的是一个目录，则该目录必须是一个空目录，删除成功将返回 true。

【例 5】删除 ch09 目录下的 9-2.jsp 文件和 Students 目录：

```
<%@page contentType="text/html;charset=utf-8" %>
<%@page import="java.io.*" %>
<html>
<body>
<%
    File f=new File("E:/apache-tomcat-6.0.13/webapps/ch09","9-2.jsp");
    File dir=new File( "E:/apache-tomcat-6.0.13/webapps/ch09","Students");
    boolean bl=f.delete();
    boolean b2=dir.delete();
%>
<P>文件<%=f.getName()%>成功删除了吗？<%=b1%>
<P>目录<%=dir.getName() %>成功删除了吗？<%=b2%>
</body>
</html>
```