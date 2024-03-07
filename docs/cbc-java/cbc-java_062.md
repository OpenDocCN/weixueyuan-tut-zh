# Java 生成随机数（random()和 random 类）

在 java 中要生成一个指定范围之内的随机数字有两种方法：一种是调用 Math 类的 random() 方法，一种是使用 Random 类。

Random 类提供了丰富的随机数生成方法，可以产生 boolean、int、long、float, byte 数组以及 double 类型的随机数，这是它与 random() 方法最大的不同之处。random() 方法只能产生 double 类型的 0~1 的随机数。

Random 类位于 java.util 包中，该类常用的有如下两个构造方法。

*   Random()：该构造方法使用一个和当前系统时间对应的数字作为种子数，然后使用这个种子数构造 Random 对象。
*   Random(long seed)：使用单个 long 类型的参数创建一个新的随机数生成器。

Random 类提供的所有方法生成的随机数字都是均匀分布的，也就是说区间内部的数字生成的概率是均等的，在表 1 中列出了 Random 类中常用的方法。

表 1 random 类的常用方法

| 方法 | 说明 |
| boolean nextBoolean() | 生成一个随机的 boolean 值，生成 true 和 false 的值概率相等 |
| double nextDouble() | 生成一个随机的 double 值，数值介于 0,1.0)，含 0 而不包含 1.0 |
| int nextlnt() | 生成一个随机的 int 值，该值介于 int 的区间，也就是 -2³¹~2³¹-1。如果 需要生成指定区间的 int 值，则需要进行一定的数学变换 |
| int nextlnt(int n) | 生成一个随机的 int 值，该值介于 [0,n)，包含 0 而不包含 n。如果想生成 指定区间的 int 值，也需要进行一定的数学变换 |
| void setSeed(long seed) | 重新设置 Random 对象中的种子数。设置完种子数以后的 Random 对象 和相同种子数使用 new 关键字创建出的 Random 对象相同 |
| long nextLong() | 返回一个随机长整型数字 |
| boolean nextBoolean() | 返回一个随机布尔型值 |
| float nextFloat() | 返回一个随机浮点型数字 |
| double nextDouble() | 返回一个随机双精度值 |

#### 例 1

下面编写一个 [Java 程序，演示如何使用 Random 类提供的方法来生成随机数。具体代码如下:

```
Import java.util.Random; public class Test06
{
    public static void main(String[] args)
    {
        Random r=new Random();
        double d1=r.nextDouble();    //生成[0,1.0]区间的小数
        double d2=r.nextDouble()*7;    //生成[0,7.0]区间的小数
        int i1=r.nextInt(10);    //生成[0,10]区间的整数
        int i2=r.nextInt(18)-3;    //生成[-3,15]区间的整数
        long l1=r.nextLong();    //生成一个随机长整型值
        boolean b1=r.nextBoolean();    //生成一个随机布尔型值
        float f1=r.nextFloat{);    //生成一个随机浮点型值
        System.out.println("生成的[0,1.0]区间的小数是："+d1);
        System.out.println("生成的[0,7.0]区间的小数是："+d2);
        System.out.println("生成的[0,10]区间的整数是："+il);
        System.out.println("生成的[-3,15]区间的整数是："+i2);
        System.out.println("生成一个随机长整型值："+l1);
        System.out.println("生成一个随机布尔型值："+b1);
        System.out.println("生成一个随机浮点型值："+fl);
        System.out.print("下期七星彩开奖号码预测：”）；
        for(int i=1;i<8;i++)
        {
            int num=r.nextlnt(9);    //生成[0,9]区间的整数
            System.out.print(num);
        }
    }
}
```

本实例每次运行时结果都不相同，这就实现了随机产生数据的功能。该程序的运行结果如下：

```
生成的[0,1.0]区间的小数是：0.8471314908854594
生成的[0,7.0]区间的小数是：3.376288548243103
生成的[0,10]区间的整数是：8
生成的[-3,15]区间的整数是：3
生成一个随机长整型值：-6309801943074175174
生成一个随机布尔型值：true
生成一个随机浮点型值：0.40940058
下期七星彩开奖号码预测：0377088
```

#### 例 2

Math 类的 random() 方法没有参数，它默认会返回大于等于 0.0、小于 1.0 的 double 类型随机数，即 0<=随机数<1.0。对 random() 方法返回的数字稍加处理，即可实现产生任意范围随机数的功能。

下面使用 random() 方法实现随机生成一个 2~100 偶数的功能。具体代码如下：

```
public class Test07
{
    public static void main(String[] args)
    {
        int min=2;    //定义随机数的最小值
        int max=102;    //定义随机数的最大值
        //产生一个 2~100 的数
        int s=(int)min+(int)(Math.random()*(max-min));
        if(s%2==0)    //如果是偶数就输出
            System.out.println("随机数是："+s);
        else    //如果是奇数就加 1 后输出
            System.out.println("随机数是："+(s+1));
    }
}
```

由于 m+(int)(Math.random()*n) 语句可以获取 m~m+n 的随机数，所以 2+(int)(Math. random()*(102-2)) 表达式可以求出 2~100 的随机数。在产生这个区间的随机数后还需要判断是否为偶数，这里使用了对 2 取余数，如果余数不是零，说明随机数是奇数，此时将随机数加 1 后再输出。

该程序的运行结果如下：

```
随机数是：20
```