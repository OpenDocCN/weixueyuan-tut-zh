# Java 图书信息查询

前面详细介绍了 Java 中各集合的使用，像 Set 集合和 List 集合等，另外，还结合泛型讲解了一些高级应用。在实际开发中，泛型集合是较常用的，一般定义集合都会使用泛型的形式来定义。本节将使用泛型集合来模拟实现某图书管理系统的查询功能。

在图书管理系统中为了方便管理图书，将图书划分为几个类别。每个类别下有很多图书，每本图书都有相对应的类别，这就具备了一对多的关系映射，即一个类别对应多本图书。

在这种情况下就可以使用 Map 映射来存储类别和图书信息，其键为 Category(类别) 类型，值为 List<Book> 类型（Book 类为图书类），然后使用嵌套循环遍历输出每个类别所对应的多个图书信息。具体的实现步骤如下。

(1) 创建表示图书类别的 Category 类，在该类中有两个属性：id 和 name，分别表示编号和类别名称，并实现了它们的 setXxx() 和 getXxx() 方法，具体内容如下：

```
public class Category
{
    private int id;    //类别编号
    private String name;    //类别名称
    public Category(int id,String name)
    {
        this.id=id;
        this.name=name;
    }
    public String toString()
    {
        return "所属分类："+this.name;
    }
    //上面两个属性的 setXxx()和 getXxx()方法
        public int getId()
    {
        return id;
    }
    public void setId(int id)
    {
        this.id=id;
    }
    public String getName()
    {
        return name;
    }
    public void setName(String name)
    {
        this.name=name;
    }
}
```

(2) 创建表示图书明细信息的 BookInfo 类，在该类中包含 5 个属性：id、name、price、author 和 startTime，分别表示图书编号、名称、价格、作者和出版时间，同样实现了它们的 setXxx() 和 getXxx() 方法，具体内容如下：

```
public class BookInfo
{
    private int id;    //编号
    private String name;    //名称
    private int price;    //价格
    private String author;    //作者
    private String startTime;    //出版时间
    public BookInfo(int id,String name,int price,String author,String startTime)
    {
        this.id=id;
        this.name=name;
        this.price=price;
        this.author=author;
        this.startTime=startTime;
    }
    public String toString()
    {
        return this.id+"\t\t"+this.name+"\t\t"+this.price+"\t\t"+this.author+"\t\t"+this.startTime;
    }
    //上面 5 个属性的 setXxx() 和 getXxx() 方法
    public int getId()
    {
        return id;
    }
    public void setId(int id)
    {
        this.id=id;
    }
    public String getName()
    {
        return name;
    }
    public void setName(String name)
    {
        this.name=name;
    }
    public int getPrice()
    {
        return price;
    }
    public void setPrice(int price)
    {
        this.id=price;
    }
    public String getAuthor()
    {
        return author;
    }
    public void setAuthor(String author)
    {
        this.author=author;
    }
    public String getStartTime()
    {
        return startTime;
    }
    public void setStartTime(String startTime)
    {
        this.startTime=startTime;
    }
}
```

(3) 创建 CategoryDao 类，在该类中定义一个泛型的 Map 映射，其键为 Category 类型的对象，值为 List<BookInfo> 类型的对象，并定义 printCategoryInfo() 方法，用于打印类别和图书明细信息。具体代码如下：

```
public class CategoryDao
{
    //定义泛型 Map，存储图书信息
    public static Map<Category,List<Bookinfo>>categoryMap=new HashMap<Category,List<BookInfo>>();
    public static void printDeptmentInfo()
    {
        for(Category cate:categoryMap.keySet())
        {
            System.out.println("所属类别："+cate.getName());
            List<BookInfo> books=categoryMap.get(cate);
            System.out.println("图书编号\t\t 图书名称\t\t 图书价格\t\t 图书作者\t\t 出版时间");
            for(int i=0;i<books.size();i++)
            {
                BookInfo b=books.get(i);    //获取图书
                System.out.println(b.getId()+"\t\t"+b.getName()+"\t\t"+b.getPrice()+"\t\t"+b.getAuthor()+"\t\t"+b.getStartTime());
            }
            System.out.println();
        }
    }
}
```

(4) 创建测试类 Test17，在该类中定义 4 个 Deptment 对象和 8 个 People 对象，并将 8 个 People 对象分成 4 组，存储到 4 个 List 集合中，然后将 4 个 Deptment 对象和 4 个 List 集合按照——对应的关系存储到 DeptmentDao 类中的 peoplesMap 映射中。最后调用 DeptmentDao 类中的 printDeptmentInfo() 方法打印类别及对应的图书信息。具体的代码如下：

```
public class Test17
{
    public static void main(String[] args)
    {
        Category category1=new Category(1,"数据库"); //创建类别信息
        Category category2=new Category(2,"程序设计");    //创建类别信息
        Category category3=new Category(3,"平面设计");    //创建类别信息
        BookInfo book1=new BookInfo(1,"细说 Java 编程",25,"张晓玲","2012-01-01");    //创建图书信息
        BookInfo book2=new BookInfo(2,"影视后期处理宝典",78,"刘水波","2012-10-05");    //创建图书信息
        BookInfo book3=new BookInfo(3,"MySQL 从入门到精通",41,"王志亮","2012-3-2");    //创建图书信息
        BookInfo book4=new BookInfo(4,"Java 从入门到精通",27,"陈奚静","2012-11-01");    //创建图书信息
        BookInfo book5=new BookInfo(5,"SQL Server 一百例",68,"张晓玲","2012-01-01");    //创建图书信息
        List<BookInfo> pList1=new ArrayList<BookInfo>();    //向类别 1 添加图书
        pList1.add(book1);
        pList1.add(book4);
        List<BookInfo> pList2=new ArrayList<BookInfo>();    //向类别 2 添加图书
        pList2.add(book3);
        pList2.add(book5);
        List<BookInfo> pList3=new ArrayList<BookInfo>();    //向类别 3 添加图书
        pList3.add(book2);
        CategoryDao.categoryMap.put(category1,pList1);
        CategoryDao.categoryMap.put(category2,pList2);
        CategoryDao.categoryMap.put(category3,pList3);
        CategoryDao.printDeptmentInfo();
    }
}
```

在该程序中，使用了泛型 List 和泛型 Map 分别存储图书类别和特定类别下的图书明细信息。从中可以看出使用泛型不仅减少了代码的编写量，也提高了类型的安全性。

运行该程序，输出的结果如下所示。

```
所属类别：数据库
图书编号        图书名称        图书价格        图书作者        出版时间
1        细说 Java 编程        25        张晓玲        2012-01-01
4        Java 从入门到精通        27        陈奚静        2012-11-01

所属类别：平面设计
图书编号        图书名称        图书价格        图书作者        出版时间
2        影视后期处理宝典        78        刘水波        2012-10-05

所属类别：程序设计
图书编号        图书名称        图书价格        图书作者        出版时间
3        MySQL 从入门到精通        41        王志亮        2012-3-2
5        SQL Server 一百例        68        张晓玲        2012-01-01
```