# Java 自增和自减运算符（++和--）

在对一个变量做加 1 或减 1 处理时，可以使用自增运算符 ++ 或自减运算 --。++ 或 -- 是单目运算符，放在操作数的前面或后面都是允许的。++ 与 -- 的作用是使变量的值增 1 或减 1。操作数必须是一个整型或浮点型变量。自增、自减运算的含义及其使用实例如表 1 所示。

表 1 自增、自减运算的含义及其使用实例

| 运算符 | 含义 | 实例 | 结果 |
| i++ | 将 i 的值先使用再加 1 赋值给 i 变量本身 | int i=1; int j=i++; | i=2 j=1 |
| ++i | 将 i 的值先加 1 赋值给变量 i 本身后再使用 | int i=1; int j=++i; | i=2 j=2 |
| i-- | 将 i 的值先使用再减 1 赋值给变量 i 本身 | int i=1; int j=i--; | i=0 j=1 |
| --i | 将 i 的值先减 1 后赋值给变量 i 本身再使用 | int i=1; int j=--i; | i=0 j=0 |

在使用自增/自减运算时应注意下面几个问题。

*   自增/自减只能作用于变量，不允许对常量、表达式或其他类型的变量进行操作。常见的错误是试图将自增或自减运算符用于非简单变量表达式中。
*   自增/自减运算可以用于整数类型 byte、short、int、long，浮点类型 float、double，以及字符串类型 char。
*   在 Java 1.5 以上版本中，自增/自减运算可以用于基本类型对应的包装器类 Byte、Short、Integer、Long、Float、Double 和 Character。
*   自增/自减运算结果的类型与被运算的变量类型相同。

#### 例 1

编写一个程序，使用不同类型的数据结合自增和自减运算符进行运算，并输出变量的值。实现代码如下：

```
public static void main(String[] args)
{
    int x=5,y;    //声明用于自增和自减的整型变量
    char cx=‘B’,cy;    //声明用于自增和自减的字符型变量
    float fx=5.5f,fy;    //声明用于自增和自减的浮点型变量
    System.out.println("---------对整数的自增和自减---------");
    y=x++;
    System.out.printf("y=x++ 的结果为：%d ,%d \n",x,y);
    y=x--;
    System.out.printf("y=x-- 的结果为：%d ,%d \n",x,y);
    y=++x;
    System.out.printf("y=++x 的结果为：%d ,%d \n",x,y);
    y=--x;
    System.out.printf("y=--x 的结果为：%d ,%d \n",x,y);
    System.out.println("\n---------对浮点的自增和自减---------");
    fy=fx++;
    System.out.printf("fy=fx++ 的结果为：%f ,%f \n",fx,fy);
    fy=fx--;
    System.out.printf("fy=fx-- 的结果为：%f ,%f \n",fx,fy);
    fy=++fx;
    System.out.printf("fy=++fx 的结果为：%f ,%f \n",fx,fy);
    fy=--fx;
    System.out.printf("fy=--fx 的结果为：%f ,%f \n",fx,fy);
    System.out.println("\n---------对字符的自增和自减---------");
    cy=cx++;
    System.out.printf("cy=cx++ 的结果为：%c ,%c \n",cx,cy);
    cy=cx--;
    System.out.printf("cy=cx-- 的结果为：%c ,%c \n",cx,cy);
    cy=++cx;
    System.out.printf("cy=++cx 的结果为：%c ,%c \n",cx,cy);
    cy=--cx;
    System.out.printf("cy=--cx 的结果为：%c ,%c \n",cx,cy);
}
```

保存代码并运行，输出的结果如下：

```
---------对整数的自增和自减---------
y=x++ 的结果为：6 ,5
y=x-- 的结果为：5 ,6
y=++x 的结果为：6 ,6
y=--x 的结果为：5 ,5

---------对浮点的自增和自减---------
fy=fx++ 的结果为：6.500000 ,5.500000
y=x-- 的结果为：5.500000 ,6.500000
y=++x 的结果为：6.500000 ,6.500000
y=--x 的结果为：5.500000 ,5.500000

---------对字符的自增和自减---------
cy=cx++ 的结果为：C ,B
cy=cx-- 的结果为：B ,C
cy=++cx 的结果为：C ,C
cy=--cx 的结果为：B ,B
```

从运行结果来看，无论是何种类型，只要是自增和自减运算符支持的类型，都可以参与运算。