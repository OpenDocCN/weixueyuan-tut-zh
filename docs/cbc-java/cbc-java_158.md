# Java 反射在类中的应用：通过反射执行方法（获取方法）

要**动态获取**一个对象方法的信息，首先需要通过下列方法之一创建一个 `Method` 类型的对象或者数组。

*   getMethods()
*   getMethods(String name,Class<?> …parameterTypes)
*   getDeclaredMethods()
*   getDeclaredMethods(String name,Class<?>...parameterTypes)

如果是访问指定的构造方法，需要根据该方法的入口参数的类型来访问。例如，访问一个名称为 max,入口参数类型依次为 int 和 String 类型的方法。

下面的两种方式均可以实现：

```
objectCiass.getDeclaredConstructor("max",int.class,String.class);
objectClass.getDeclaredConstructor("max",new Ciass[]{int.class,String.class});
```

Method 类的常用方法如表 3 所示。

表 1 Method 类的常用方法

| 静态方法名称 | 说明 |
| getName() | .获取该方法的名称 |
| getParameterType() | 按照声明顺序以 Class 数组的形式返回该方法各个参数的类型 |
| getRetumType() | 以 Class 对象的形式获得该方法的返回值类型 |
| getExceptionTypes() | 以 Class 数组的形式获得该方法可能抛出的异常类型 |
| invoke(Object obj,Object...args) | 利用 args 参数执行指定对象 obj 中的该方法，返回值为 Object 类型 |
| isVarArgs() | 查看该方法是否允许带有可变数量的参数，如果允许返回 true，否 则返回 false |
| getModifiers() | 获得可以解析出该方法所采用修饰符的整数 |

#### 例 2

下面通过一个案例来演示如何调用 Method 类的方法获取动态类中方法的信息。

(1) 首先创建一个 Book1 类，并编写 4 个具有不同作用域的方法。Book1 类的最终代码如下：

```
package ch12;
public class Book1
{
    //static 作用域方法
    static void staticMethod()
    {
        System.out.println("执行 staticMethod()方法");
    }
    //public 作用域方法
    public int publicMethod(int i)
    {
        System.out.println("执行 publicMethod()方法");
        return 100+i;
    }
    //protected 作用域方法
    protected int protectedMethod(String s,int i)throws NumberFormatException
    {
        System.out.println("执行 protectedMethod()方法");
        return Integer.valueOf(s)+i;
    }
    //private 作用域方法
    private String privateMethod(String ...strings)
    {
        System.out.println("执行 privateMethod()方法");

        StringBuffer sb=new StringBuffer();
        for(int i=0;i<sb.length();i++)
        {
            sb.append(strings[i]);
        }
        return sb.toString();
    }
}
```

(2) 编写测试类 Test02，在该类的 main() 方法中通过反射访问 Book1 类中的所有方法，并将该方法是否带可变类型参数、入口参数类型和可能拋出的异常类型信息输出到控制台。

Test02 类的代码如下：

```
package ch12;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
public class Test02
{
    public static void main(String[] args)
    {
        //获取动态类 Book1
        Book1 book=new Book1();
        Class class1=book.getClass();
        //获取 Book1 类的所有方法
        Method[] declaredMethods= class1.getDeclaredMethods();
        for(int i=0;i<declaredMethods.length;i++)
        {
            Method method=declaredMethods[i];
            System.out.println("方法名称为："+method.getName());
            System.out.println("方法是否带有可变数量的参数："+method.isVarArgs());
            System.out.println("方法的参数类型依次为：");
            //获取所有参数类型
            Class[] methodType=method.getParameterTypes();
            for(int j=0;j<methodType.length;j++)
            {
                System.out.println(" "+methodType[j]);
            }
            //获取返回值类型
            System.out.println("方法的返回值类型为："+method.getReturnType());
            System.out.println("方法可能抛出的异常类型有：");
            //获取所有可能抛出的异常
            Class[] methodExceptions=method.getExceptionTypes();
            for(int j=0;j<methodExceptions.length;j++)
            {
                System.out.println(" "+methodExceptions[j]);
            }
            boolean isTurn=true;
            while(isTurn)
            {
                try
                {    //如果该成员变量的访问权限为 private，则抛出异常
                    isTurn =false;
                    if(method.getName().equals("staticMethod"))
                    {    //调用没有参数的方法
                        method.invoke(book);
                    }
                    else if(method.getName().equals("publicMethod"))
                    {    //调用一个参数的方法
                        System.out.println("publicMethod(10)的返回值为："+method.invoke(book,10));
                    }
                    else if(method.getName().equals("protectedMethod"))
                    {    //调用两个参数的方法
                        System.out.println("protectedMethod(\"10\",15)的返回值为："+method.invoke(book,"10",15));
                    }
                    else if(method.getName().equals("privateMethod"))
                    {    //调用可变数量参数的方法
                        Object[] parameters=new Object[]{new String[]{"J","A","V","A"}};
                        System.out.println("privateMethod()的返回值为："+method.invoke(book,parameters));
                    }
                }
                catch (Exception e)
                {
                    System.out.println("在设置成员变量值时抛出异常，下面执行 setAccessible()方法");
                    method.setAccessible(true);    //设置为允许访问 private 方法
                    isTurn = true;

                }
            }
            System.out.println("=============================\n");
        }
    }
}
```

(3) 运行测试类 test02，程序将会依次动态访问 Book1 类中的所有方法。访问 staticMethod() 方法的运行效果如下所示：

```
方法名称为：staticMethod
方法是否带有可变数量的参数：false
方法的参数类型依次为：
方法的返回值类型为：void
方法可能抛出的异常类型有：
执行 staticMethod()方法
=============================
```

访问 publicMethod() 方法的运行效果如下所示：

```
方法名称为：publicMethod
方法是否带有可变数量的参数：false
方法的参数类型依次为：
int
方法的返回值类型为：int
方法可能抛出的异常类型有：
执行 publicMethod()方法
publicMethod(10)的返回值为：110
=============================
```

访问 protectedMethod() 方法的运行效果如下所示：

```
方法名称为：protectedMethod
方法是否带有可变数量的参数：false
方法的参数类型依次为：
class java.lang.String
int
方法的返回值类型为：int
方法可能抛出的异常类型有：
class java.lang.NumberFormatException
执行 protectedMethod()方法
protectedMethod("10",15)的返回值为：25
=============================
```

访问 privateMethod() 方法的运行效果如下所示：

```
方法名称为：privateMethod
方法是否带有可变数量的参数：true
方法的参数类型依次为：
class java.lang.String;
方法的返回值类型为：class java.lang.String
方法可能抛出的异常类型有：
在设置成员变量值时抛出异常，下面执行 setAccessible()方法
执行 privateMethod()方法
privateMethod()的返回值为：
=============================
```