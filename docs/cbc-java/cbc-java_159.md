# Java 反射在类中的应用：通过反射访问成员变量

通过下列任意一个方法访问成员变量时将返回 Field 类型的对象或数组。

*   getFields()
*   getField(String name)
*   getDeclaredFields()
*   getDeclaredField(String name)

上述方法返回的 Field 对象代表一个成员变量。例如，要访问一个名称为 price 的成员变量，示例代码如下：

```
objectgetDeciaredField("price");
```

Field 类的常用方法如表 1 所示

表 1 Field 类的常用方法

| 方法名称 | 说明 |
| getName() | 获得该成员变量的名称 |
| getType() | 获取表示该成员变量的 Class 对象 |
| get(Object obj) | 获得指定对象 obj 中成员变量的值，返回值为 Object 类型 |
| set(Object obj,Object value) | 将指定对象 obj 中成员变量的值设置为 value |
| getlnt(0bject obj) | 获得指定对象 obj 中成员类型为 int 的成员变量的值 |
| setlnt(0bject obj,int i) | 将指定对象 obj 中成员变量的值设置为 i |
| setFloat(Object obj,float f) | 将指定对象 obj 中成员变量的值设置为 f |
| getBoolean(Object obj) | 获得指定对象 obj 中成员类型为 boolean 的成员变量的值 |
| setBoolean(Object obj,boolean b) | 将指定对象 obj 中成员变量的值设置为 b |
| getFloat(Object obj) | 获得指定对象 obj 中成员类型为 float 的成员变量的值 |
| setAccessible(boolean flag) | 此方法可以设置是否忽略权限直接访问 private 等私有权限的成员变量 |
| getModifiers() | 获得可以解析出该方法所采用修饰符的整数 |

#### 例 1

下面通过一个案例来演示如何调用 Field 类的方法获取动态类中各个成员的信息。

(1) 首先创建一个 Book2 类，在该类中依次声明一个 String、int、float 和 boolean 类型的成员，并设置不同的访问作用域。Book2 类最终的代码如下：

```
package ch12;
public class Book2
{
    String name;
    public int id;
    private float price;
    protected boolean isLoan;
}
```

(2) 编写测试类 Test03，在该类的 main() 方法中通过反射访问 Book2 类中的所有成员，并将该成员的名称和类型信息输出到控制台。

Test03 类的代码如下：

```
package ch12;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
public class Test03
{
    public static void main(String[] args)
    {
        Book2 book=new Book2();
        //获取动态类 Book2
        Class class1=book.getClass();
        //获取 Book2 类的所有成员
        Field[] declaredFields=class1.getDeclaredFields();
        //遍历所有的成员
        for(int i=0;i<declaredFields.length;i++)
        {    //获取类中的成员变量
            Field field=declaredFields[i];
            System.out.println("成员名称为："+field.getName());
            Class fieldType=field.getType();
            System.out.println("成员类型为："+fieldType);
            boolean isTurn=true;
            while(isTurn)
            {
                try
                {    //如果该成员变量的访问权限为 private，则抛出异常
                    isTurn=false;
                    System.out.println("修改前成员的值为："+field.get(book));
                    //判断成员类型是否为 int
                    if(fieldType.equals(int.class))
                    {
                        System.out.println("利用 setInt()方法修改成员的值");
                        field.setInt(book, 100);
                    }
                    else if(fieldType.equals(float.class))
                    {    //判断成员变量类型是否为 float
                        System.out.println("利用 setFloat()方法修改成员的值");
                        field.setFloat(book, 29.815f);
                    }
                    else if(fieldType.equals(boolean.class))
                    {    //判断成员变量是否为 boolean
                        System.out.println("利用 setBoolean()方法修改成员的值");
                        field.setBoolean(book, true);
                    }
                    else
                    {
                        System.out.println("利用 set()方法修改成员的值");
                        field.set(book, "Java 编程");
                    }
                    System.out.println("修改后成员的值为：" + field.get(book));
                } catch (Exception e)
                {
                    System.out.println("在设置成员变量值时抛出异常，下面执行 setAccessible()方法");
                    field.setAccessible(true);
                    isTurn=true;
                }
            }
            System.out.println("=============================\n");
        }
    }
}
```

(3) 运行测试类 Test03，程序将会依次动态访问 Book2 类中的所有成员。访问 name 成员的运行效果如下所示：

```
成员名称为：name
成员类型为：class java.lang.String
修改前成员的值为：null
利用 set()方法修改成员的值
修改后成员的值为：Java 编程
=============================
```

访问 id 成员的运行效果如下所示：

```
成员名称为：id
成员类型为：int
修改前成员的值为：0
利用 setInt()方法修改成员的值
修改后成员的值为：100
=============================
```

访问 price 成员的运行效果如下所示：

```
成员名称为：price
成员类型为：float
在设置成员变量值时抛出异常，下面执行 setAccessible()方法
修改前成员的值为：0.0
利用 setFloat()方法修改成员的值
修改后成员的值为：29.815
=============================
```

访问 isLoan 成员的运行效果如下所示：

```
成员名称为：isLoan
成员类型为：boolean
修改前成员的值为：false
利用 setBoolean()方法修改成员的值
修改后成员的值为：true
=============================
```