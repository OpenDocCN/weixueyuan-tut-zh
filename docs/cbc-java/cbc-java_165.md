# Java RandomAccessFile 类：动态读取文件内容

所谓动态读取是指从文件的任意位置开始访问文件，而不是必须从文件开始位置读取到文件末尾。动态读取需要用到 Java 中的 RandomAccessFile 类，该类中有一个文件指针用于标识当前流的读写位置，这个指针可以向前或者向后移动。

RandomAccessFile 类的构造方法有如下两种重载形式。

1.  RandomAccessFile(File file,String mode)：访问参数 file 指定的文件，访问形式由参数 mode 指定，mode 参数有两个常用的可选值`r`和`rw`，其中`r`表示只读，`rw`表示读写。
2.  RandomAccessFile(String name,String mode)：访问参数 name 指定的文件，mode 参数的含义同上。

RandomAccessFile 类中还提供了一系列读取和写入数据的方法，表 1 列举了其中一些 常用方法。

表 1 RandomAccessFile 类的常用方法

| 方法名及返回值类型 | 说明 |
| boolean readBoolean() | 从文件中读取一个 boolean 值 |
| byte readByte() | 从文件中读取一个带符号位的字节 |
| char readChar() | 从文件中读取一个字符 |
| int readlnt() | 从文件中读取一个带符号位的整数 |
| long readLong() | 从文件中读取一个带符号位的 long 值 |
| String readLine() | 从文件中读取下一行文本 |
| void seek(long pos) | 指定从文件起始位置开始的指针偏移量 |
| void writeBoolean(boolean v) | 以字节的形式向文件中写入一个 boolean 值 |
| void writeByte(int v) | 以单字节的形式向文件中写入一个 byte 值 |
| void writeChar(int v) | 以双字节的形式向文件中写入一个 char 值 |
| void writelnt(int v) | 以 4 字节的形式向文件中写入一个整数 |
| writeLong(long v) | 以 8 字节的形式向文件中写入一个 long 值 |
| void writeBytes(String s) | 以字节序列的形式向文件中写入一个字符串 |
| void skipBytes(int n) | 以当前文件指针位置为起始点，跳过 n 字节 |

#### 例 1

编写一个程序，使用 RandomAccessFileDemo 类创建一个 words.txt 文件，然后写入一个长中文字符串，再从第 4 个字节开始读取并输出。

(1) 创建一个 RandomAccessFileDemo 类对象。在 main() 方法中创建到 D:\JavaCodes\ words.txt 的 File 对象，如果该文件已经存在则先删除再创建，代码如下所示。

```
public class RandomAccessFileDemo
{
    public static void main(String[] args)
    {
        try
        {
            File file=new File("D:\\myJava\\words.txt");    //指定文件路径
            if(file.exists())
            {   //判断文件是否存在
                file.delete();
                file.createNewFile();
            }
        }
        catch(IOException e)
        {
            System.out.print(e);
        }
    }
}
```

(2) 创建 RandomAccessFile 对象，以读写方式操作 File 对象。定义一个要写入的字符串，再将其进行格式的转换。这样是为了使其写入文件的内容不出现乱码，再将转换后的内容写入文件，代码如下所示。

```
RandomAccessFile raf=new RandomAccessFile(file,"rw");
String str1="晴天，阴天，多云，小雨，大风，中雨，小雪，雷阵雨";    //要写入的字符串
String str2=new String(str1.getBytes("GBK"),"iso8859-1");    //编码转换
raf.writeBytes(str2);    //写入文件
```

(3) 打印出当前指针的位置，然后将其移动到第 4 个字节。再定义一个长度为 2 的 byte 数组，然后开始进行内容的循环读取，将读出的内容以字符串的形式输出到控制台，代码如下所示。

```
System.out.println("当前文件指针的位置："+raf.getFilePointer());
raf.seek(6);    //移动文件指针
System.out.println("从文件头跳过 6 个字节，现在文件内容如下：");
byte[] buffer=new byte[2];
int len=0;
while((len=raf.read(buffer,0,2))!=-1)
{
    System.out.print(new String(buffer,0,len));    //输出文件内容
}
```

(4) 运行程序，程序运行结果如下所示，显示了写入字符串后指针的位置，以及从文件开关跳过 6 个字节后读取到的字符串，图 1 为写入文件中的字符串内容。

```
当前文件指针的位置：48
从文件头跳过 6 个字节，现在文件内容如下：
阴天，多云，小雨，大风，中雨，小雪，雷阵雨
```

![](img/10232159b19e15c43d67f707a9ed7e41.jpg)
图 1 word.txt 文件内容
对比输出结果和图 1 发现，中文字符串已成功写入记事本中，但是读出的字符串内容少了两个字和一个逗号，这是由于使用 RandomAccessFile 的 seek() 方法跳过了前 6 字节（1 个汉字占两字节）。在本程序中将中文字符串进行了编码转换，然后再写入文件。读取时调用的是带有 3 个参数的 read() 方法将记事本中的内容读取出来。

注意：要写进文本的内容是中文，如果不进行字符的转换，写进去的会是乱码，读取出来的内容也会是乱码。