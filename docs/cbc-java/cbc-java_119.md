# Java 封装图书信息类

了解上节有关封装的知识后，本节通过完整的例子再次实现封装。要求编写表示图书的 Book 类，实现以下需求：

*   基本信息包括图书名称（bookName）、总页数（pagelbtalNum），其中页数不能少于 200 页，否则输出错误信息，并赋予默认值 200。
*   为各个属性设置赋值和取值方法。
*   具有 details() 方法，该方法在控制台输出每本图书的名称和总页数。

编写 BookTest 测试类，为 Book 对象的属性赋予初始值，并调用 details() 方法输出详细信息。根据上面的描述添加代码，步骤如下。

(1) 创建 Book 类，首先向该类添加 bookName 变量，并封装该变量。代码如下：

```
private String bookName;    //图书名称
public String getBookName()
{
    return bookName;
}
public void setBookName(String bookName)
{
    this.bookName=bookName;
}
```

(2) 在 Book 类中添加 bookTotalNum 变量，并封装该变量，在封装的 setter 方法中判断页数的值是否小于 200。代码如下：

```
private int bookTotalNum;    //图书总页数
public int getBookTotaiNum()
{
    return bookTotalNum;
}
public void setBookTotalNum(int bookTotalNum)
{
    if(bookTotalNum<200)
    {
        System.out.println(this.bookName+"这本书的页数不能少于 200 页");
        this.bookTotalNum=200;
    }
    else
    {
        this.bookTotalNum=bookTotalNum;
    }
}
```

(3) 在 Book 类中添加公有的 details() 方法，输出图书的名称和总页数。代码如下：

```
public void details()
{
    System.out.println(this.bookName+"这本书的总页数是："+this.bookTotalNum);
}
```

(4) 创建 BookTest 测试类，在该类的 main() 方法中创建 Book 类的两个实例对象，然后分别为类中的两个属性赋值，最后调用 details() 方法输出信息。代码如下：

```
public class BookTest
{
    public static void main(String[] args)
    {
        Book book1=new Book();
        book1.setBookName("《红与黑》");
        book1.setBookTotalNum(190);
        book1.details();
        System.out.println("************************************");
        Book book2=new Book();
        book2.setBookName("《格林童话》");
        book2.setBookTotalNum(520);
        book2.details();
    }
}
```

(5) 执行上述代码，输出结果如下：

```
《红与黑》这本书的页数不能少于 200 页
《红与黑》这本书的总页数是：200
************************************
《格林童话》这本书的总页数是：520
```