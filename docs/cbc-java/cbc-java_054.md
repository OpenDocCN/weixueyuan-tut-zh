# Java 字符串的加密解密

为了保证程序的安全，经常采用数据加密的方法。Java 中提供了专门用于加密运算的类和接口。

除了使用加密类和接口外，还可以通过多种方式实现字符串的加密。其中常用的就是获取字符串的字节数组，之后对字节数组中的每个字节都进行运算，得到新的内容，这时所获得的字符串与原字符串将不相同，以此达到加密的效果；解密时再将加密字符串进行相反的运算，这样即可得到原字符串。

本实例简单介绍如何通过数据运算实现字符串的加密和解密，实现步骤如下。

(1) 创建一个静态的 encryptAndDencrypt() 方法，在该方法中传入两个参数。代码如下：

```
public static String encryptAndDencrypt(String value, char secret)
{
    byte[] bt=value.getBytes();    //将需要加密的内容转换为字节数组
    for(int i=0;i<bt.length;i++)
    {
        bt[i]=(byte)(bt[i]^(int)secret);    //通过异或运算进行加密
    }
    String newresult=new String(bt,0,bt.length);    //将加密后的字符串保存到 newresult 变量中
    return newresult;
}
```

上述代码首先将需要加密的内容转换为字节数组，接着遍历字节数组中的内容，在 for 语句中通过异或运算进行加密，然后将加密后的字符串保存到 newresult 变量中。最后返回 newresult 变量的值。

(2) 在 main() 方法中添加代码，接收用户在控制台输入的内容并输出，然后调用 encryptAndUncrypt() 方法对字符串分别进行加密和解密，并将加密和解密后的内容输出。代码如下：

```
public static void main(String[] args)
{
    Scanner scan=new Scanner(System.in);
    char secret='8';    //加密文字符
    System.out.println("请输入您想加密的内容：");
    String pass=scan.next();
    System.out.println("原字符串内容："+pass);
    String encryptResult=encryptAndDencrypt(pass,secret);
    System.out.println("加密后的内容："+encryptResult);
    String uncryptResult=encryptAndDencrypt(encryptResult,secret);
    System.out.println("解密后的内容："+uncryptResult);
}
```

(3) 执行上述代码进行测试，如下所示。

```
请输入您想加密的内容：
hello,java
原字符串内容：hello,java
加密后的内容：P]TTW�RYNY
解密后的内容：hello,java
```