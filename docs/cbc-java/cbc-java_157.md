# Java 反射在类中的应用：通过反射访问构造函数（方法）

为了能够**动态获取**对象构造方法的信息，首先需要通过下列方法之一创建一个 `Constructor` 类型的对象或者数组。

*   getConstructors()
*   getConstructor(Class<?>…parameterTypes)
*   getDeclaredConstructors()
*   getDeclaredConstructor(Class<?>...parameterTypes)

如果是访问指定的构造方法，需要根据该构造方法的入口参数的类型来访问。例如，访问一个入口参数类型依次为 int 和 String 类型的构造方法，下面的两种方式均可以实现。

```
objectClass.getDeclaredConstructor(int.class,String.class);
objectClass.getDeclaredConstructor(new Class[]{int.class,String.class});
```

创建的每个 Constructor 对象表示一个构造方法，然后利用 Constructor 对象的方法操作构造方法。Constructor 类的常用方法如表 1 所示。

表 1 Constructor 类的常用方法

| 方法名称 | 说明 |
| isVarArgs() | 查看该构造方法是否允许带可变数量的参数，如果允许，返回 true，否则返回 false |
| getParameterTypes() | 按照声明顺序以 Class 数组的形式获取该构造方法各个参数的类型 |
| getExceptionTypes() | 以 Class 数组的形式获取该构造方法可能抛出的异常类型 |
| newInstance(Object … initargs) | 通过该构造方法利用指定参数创建一个该类型的对象，如果未设置参数则表示 采用默认无参的构造方法 |
| setAccessiable(boolean flag) | 如果该构造方法的权限为 private，默认为不允许通过反射利用 netlnstance() 方法创建对象。如果先执行该方法，并将入口参数设置为 true，则允许创建对
象 |
| getModifiers() | 获得可以解析出该构造方法所采用修饰符的整数 |

通过 java.lang.reflect.Modifier 类可以解析出 getMocMers() 方法的返回值所表示的修饰符信息。在该类中提供了一系列用来解析的静态方法，既可以查看是否被指定的修饰符修饰，还可以字符串的形式获得所有修饰符。表 2 列出了 Modifier 类的常用静态方法。

表 2 Modifier 类的常用方法

| 静态方法名称 | 说明 |
| isStatic(int mod) | 如果使用 static 修饰符修饰则返回 true，否则返回 false |
| isPublic(int mod) | 如果使用 public 修饰符修饰则返回 true，否则返回 false |
| isProtected(int mod) | 如果使用 protected 修饰符修饰则返回 true，否则返回 false |
| isPrivate(int mod) | 如果使用 private 修饰符修饰则返回 true，否则返回 false |
| isFinal(int mod) | 如果使用 final 修饰符修饰则返回 true，否则返回 false |
| toString(int mod) | 以字符串形式返回所有修饰符 |

例如，下列代码判断对象 con 所代表的构造方法是否被 public 修饰，以及以字符串形式获取该构造方法的所有修饰符。

```
int modifiers=con.getModifiers();    //获取构造方法的修饰符整数
boolean isPubiic=Modifier.isPublic(modifiers);    //判断修饰符整数是否为
public string ailModifSers=Modifier.toString(modifiers);
```

#### 例 1

下面通过一个案例来演示如何调用 Constructor 类的方法获取构造方法的信息。

(1) 首先创建一个 Book 类表示图书信息。在该类中声明一个 String 型变量表示图书名称，两个 int 型变量分别表示图书编号和价格，并提供 3 个构造方法。

Book 类的最终代码如下：

```
public class Book
{
    String name;    //图书名称
    int id,price;    //图书编号和价格
    //空的构造方法
    private Book(){}
    //带两个参数的构造方法
    protected Book(String _name,int _id)
    {
        this.name=_name;
        this.id=_id;
    }
    //带可变参数的构造方法
    public Book(String...strings)throws NumberFormatException
    {
        if(0<strings.length)
            id=Integer.valueOf(strings[0]);
        if(1<strings.length)
            price=Integer.valueOf(strings[1]);
    }
    //输出图书信息
    public void print()
    {
        System.out.println("name="+name);
        System.out.println("id="+id);
        System.out.println("price="+price);
    }
}
```

(2) 编写测试类 Test01，在该类的 main() 方法中通过反射访问 Book 类中的所有构造方法，并将该构造方法是否带可变类型参数、入口参数类型和可能拋出的异常类型信息输出到控制台。

Test01 类的代码如下：

```
import java.lang.reflect.Constructor;
public class Test01
{
    public static void main(String[] args)
    {
        //获取动态类 Book
        Class book=Book.class;
        //获取 Book 类的所有构造方法
        Constructor[] declaredContructors=book.getDeclaredConstructors();
        //遍历所有构造方法
        for(int i=0;i<declaredContructors.length;i++)
        {
            Constructor con=declaredContructors[i];
            //判断构造方法的参数是否可变
            System.out.println("查看是否允许带可变数量的参数："+con.isVarArgs());
            System.out.println("该构造方法的入口参数类型依次为：");
            //获取所有参数类型
            Class[] parameterTypes=con.getParameterTypes();
            for(int j=0;j<parameterTypes.length;j++)
            {
                System.out.println(" "+parameterTypes[j]);
            }
            System.out.println("该构造方法可能拋出的异常类型为：");
            //获取所有可能拋出的异常类型
            Class[] exceptionTypes=con.getExceptionTypes();
            for(int j=0;j<exceptionTypes.length;j++)
            {
                System.out.println(" "+parameterTypes[j]);
            }
            //创建一个未实例化的 Book 类实例
            Book book1=null;
            while(book1==null)
            {
                try
                {    //如果该成员变量的访问权限为 private，则拋出异常
                    if(i==1)
                    {
                        //通过执行带两个参数的构造方法实例化 book1
                        book1=(Book)con.newInstance("Java 教程",10);
                    }
                    else if(i==2)
                    {
                        //通过执行默认构造方法实例化 book1
                        book1=(Book)con.newInstance();
                    }
                    else
                    {
                        //通过执行可变数量参数的构造方法实例化 book1
                        Object[] parameters=new Object[]{new String[]{"100","200"}};
                        book1=(Book)con.newInstance(parameters);
                    }
                }
                catch(Exception e)
                {
                    System.out.println("在创建对象时拋出异常，下面执行 setAccessible() 方法");
                    con.setAccessible(true);    //设置允许访问 private 成员
                }
            }
            book1.print();
            System.out.println("=============================\n");
        }
    }
}
```

(3) 运行测试类 Test01，当通过反射访问默认构造方法 Book() 时，将看到如下所示的输出。

```
查看是否允许带可变数量的参数：false
该构造方法的入口参数类型依次为：
该构造方法可能抛出的异常类型为：
在创建对象时抛出异常，下面执行 setAccessible()方法
name=null
id=0
price=0
=============================
```

当通过反射访问两个参数的构造方法 Book(String_name,int_id) 时，将看到如下所示的输出。

```
查看是否允许带可变数量的参数：false
该构造方法的入口参数类型依次为：
class java.lang.String
int
该构造方法可能抛出的异常类型为：
在创建对象时抛出异常，下面执行 setAccessible()方法
name=null
id=0
price=0
=============================
```

当通过反射访问可变参数数量的构造方法 Book(String...strings) 时，将看到如下所示的输出。

```
查看是否允许带可变数量的参数：true
该构造方法的入口参数类型依次为：
class java.lang.String;
该构造方法可能抛出的异常类型为：
class java.lang.String;
在创建对象时抛出异常，下面执行 setAccessible()方法
name=null
id=0
price=0
=============================
```