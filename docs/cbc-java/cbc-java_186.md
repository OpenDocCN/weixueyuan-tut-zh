# Java InetAddress 类及其常用方法

Internet 上的主机有两种方式表示地址，分别为域名和 IP 地址。java.net 包中的 InetAddress 类对象包含一个 Internet 主机地址的域名和 IP 地址。

InetAddress 类提供了操作 IP 地址的各种方法。该类本身没有构造方法，而是通过调用相关静态方法获取实例。InetAddress 类中的常用方法如下表 所示。

表 1 InetAddress 类的常用方法

| 方法名称 | 说明 |
| boolean equals(Object obj) | 将此对象与指定对象比较 |
| byte[] getAddress() | 返回此 InetAddress 对象的原始 IP 地址 |
| static InetAddress[] getAHByName(String host) | 在给定主机名的情况下，根据系统上配置的名称，服务器返 回其 IP 地址所组成的数组 |
| static InetAddress getByAddress(byte[] addr) | 在给定原始 IP 地址的情况下，返回 InetAddress 对象 |
| static InetAddress getByAddress(String host) | 在给定主机名的情况下确定主机的 IP 地址 |
| String getCanonicalHostName() | 获取此 IP 地址的完全限定域名 |
| String getHostAddress() | 返回 IP 地址字符串（以文本表现形式） |
| String getHostName() | 返回此 IP 地址的主机名 |
| static InetAdderss getLocalHost() | 返回本地主机 |

#### 例 1

编写程序练习 InetAddress 类的基本使用方法，主要步骤如下所示。

(1) 创建一个类。在 main() 方法中创建一个 InetAddress 对象，调用 getByName() 方法并传递参数“www.qqxom”输出此对象的 IP 地址字符串和主机名，代码如下所示。

```
public static void main(String[] args)
{       
    try
    {
        InetAddress ia1=InetAddress.getByName("www.qq.com");
        System.out.println(ia1.getHostName());
        System.out.println(ia1.getHostAddress());
    }
    catch(UnknownHostException e)
    {
        e.printStackTrace();
    }       
}
```

(2) 在 main() 方法中添加代码，创建一个 InetAddress 对象，调用 getByName() 方法并传递参数“61.135.169.105”输出此对象的 IP 地址字符串和主机名，代码如下所示。

```
try
{
    InetAddress ia2=InetAddress.getByName("61.135.169.105");
    System.out.println(ia2.getHostName());
    System.out.println(ia2.getHostAddress());
}
catch(UnknownHostException e)
{
    e.printStackTrace();
}
```

(3) 创建一个 InetAddress 对象用于获取本地主机的信息，输出此对象的 IP 地址字符串和主机名，代码如下所示。

```
try
{
    InetAddress ia3=InetAddress.getLocalHost();
    System.out.println("主机名："+ia3.getHostName());
    System.out.println("本地 ip 地址："+ia3.getHostAddress());
}
catch(UnknownHostException e)
{
    e.printStackTrace();
}
```

(4) 执行程序，运行结果如下所示。

```
www.qq.com
123.151.137.18
61.135.169.105
61.135.169.105
主机名：WQ-20161107KCPN
本地 ip 地址：192.168.0.102
```

注意：在上述代码中包含互联网的地址，所以运行时需要连网，否则会出现异常。