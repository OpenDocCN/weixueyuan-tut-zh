# Java Character 类

Character 类是字符数据类型 char 的包装类。Character 类的对象包含类型为 char 的单个字段，这样能把基本数据类型当对象来处理，其常用方法如表 1 所示。

表 1 Character 类的常用方法

| 方法 | 描述 |
| void Character(char value) | 构造一个新分配的 Character 对象，用以表示指定的 char 值 |
| char charValue() | 返回此 Character 对象的值，此对象表示基本 char 值 |
| int compareTo(Character anotherCharacter) | 根据数字比较两个 Character 对象 |
| boolean equals(Character anotherCharacter) | 将此对象与指定对象比较，当且仅当参数不是 null，而 是一个与此对象 包含相同 char 值的 Character 对象时， 结果才是 true |
| boolean isDigit(char ch) | 确定指定字符是否为数字，如果通过 Character. getType(ch) 提供的字 符的常规类别类型为 DECIMAL_DIGIT_NUMBER，则字符为数字 |
| boolean isLetter(int codePoint) | 确定指定字符（Unicode 代码点）是否为字母 |
| boolean isLetterOrDigit(int codePoint) | 确定指定字符（Unicode 代码点）是否为字母或数字 |
| boolean isLowerCase(char ch) | 确定指定字符是否为小写字母 |
| boolean isUpperCase(char ch) | 确定指定字符是否为大写字母 |
| char toLowerCase(char ch) | 使用来自 UnicodeData 文件的大小写映射信息将字符参数转换为小写 |
| char toUpperCase(char ch) | 使用来自 UnicodeData 文件的大小写映射信息将字符参数转换为大写 |

可以从 char 值中创建一个 Character 对象。例如，下列语句为字符 S 创建了一个 Character 对象。

```
Character character=new Character'S');
```

CompareTo() 方法将这个字符与其他字符比较，并且返回一个整型数组，这个值是两个字符比较后的标准代码差值。当且仅当两个字符相同时，equals() 方法的返回值才为 true。如下面的代码：

```
Character character=new Character'A');
int result1=character.compareTo(new Character('V'));
System.out.println(result1);    // 输出：0
int result2=character.compareTo(new Character('B'));
System.out.println(resuit2);    //输出：-1
int result3=character.compareTo(new Character('1'));
System.out.println(result3);    //输出：-2
```

#### 例 1

在注册会员时，需要验证用户输入的用户名、密码、性别、年龄和邮箱地址等信息是否符合标准，如果符合标准方可进行注册。那么，下面就使用 Character 类中的一些静态方法来完成这个程序，具体的实现步骤如下。

(1) 创建 Register 类，在该类中创建 validateUser() 方法，对用户输入的用户名、密码和年龄进行验证，代码如下：

```
public class Register
{
    public static boolean validateUser(String uname,String upwd,String age)
    {
        boolean conUname=false;       //用户名是否符合要求
        boolean conPwd=false;    //密码是否符合要求
        boolean conAge=false;    //年龄是否符合要求
        boolean con=false;    //验证是否通过
        if(uname.length()>0)
        {
            for(int i=0;i<uname.length();i++)
            {
                //验证用户名是否全部为字母，不能含有空格
                if(Character.isLetter(uname.charAt(i)))
                {
                    conUname=true;
                }
                else
                {
                    conUname=false;
                    System.out.println("用户名只能由字母组成，且不能含有空格！");
                    break;
                }
            }
        }
        else
        {
            System.out.println("用户名不能为空！");
        }
        if(upwd.length()>0)
        {
            for(int j=0;j<upwd.length();j++)
            {
                //验证密码是否由数字和字母组成，不能含有空格
                if(Character.isLetterOrDigit(upwd.charAt(j)))
                {
                    conPwd=true;
                }
                else
                {
                    conPwd=false;
                    System.out.println("密码只能由数字或字母组成！");
                    break;
                }
            }
        }
        else
        {
            System.out.println("密码不能为空！");
        }
        if(age.length()>0)
        {
            for(int k=0;k<age.length();k++)
            {
                //验证年龄是否由数字组成
                if(Character.isDigit(age.charAt(k)))
                {
                    conAge=true;
                }
                else
                {
                    conAge=false;
                    System.out.println("年龄输入有误!");
                    break;
                }
            }
        }
        else
        {
            System.out.println("年龄必须输入！");
        }
        if(conUname&&conPwd&&conAge)
        {
            con=true;
        }
        else
        {
            con=false;
        }
        return con;
    }
}
```

在 validateUser() 方法中，使用 for 循环遍历用户输入的用户名、密码和年龄，对其每个字符进行验证，判断其是否符合要求。在验证的过程中，分别使用了 Character 类的 isLetter() 方法、isLetterOrDigit() 方法和 isDigit() 方法。

(2) 编写测试类 Test04，调用 Register 类中的 validateUser() 方法，对用户输入的数据进行验证，并输出验证结果，代码如下：

```
import java.util.Scanner;
public class Test04
{
    public static void main(String[] args)
    {
        Scanner input=new Scanner(System.in);
        System.out.println("------------用户注册--------------");
        System.out.println("用户名：");
        String username=input.next();
        System.out.println("密码：");
        String pwd=input.next();
        System.out.println("年龄：");
        String age=input.next();
        boolean con=Register.validateUser(username,pwd,age);
        if(con)
        {
            System.out.println("注册成功！");
        }
        else
        {
            System.out.println("注册失败！");
        }
    }
}
```

在 main() 方法中，通过调用 Register 类的 validateUser() 方法，获取一个 boolean 类型的变量，即表明是否验证通过。当变量值为 true 时，表示验证通过，输出注册成功的提示信息；否则表示验证未通过，输出注册失败的提示信息。

运行该程序，当注册的用户名并非全部由字母组成时，提示“用户名只能由字母组成， 且不能含有空格！”信息，如下所示。

```
------------用户注册--------------
用户名：
tg_xiake
密码：
xiake
年龄：
123
用户名只能由字母组成，且不能含有空格！
注册失败！
```

当注册的年龄并非只有数字组成时，则提示“年龄输入有误！”，如下所示。

```
------------用户注册--------------
用户名：
admin
密码：
admin
年龄：
123a
年龄输入有误！
注册失败！
```

当注册的密码并非只由数字或字母组成时，提示“密码只能由数字或字母组成！”，如下所示。

```
------------用户注册--------------
用户名：
admin
密码：
admin!
年龄：
25
密码只能由数字或字母组成！
注册失败！
```

如果注册的用户名、密码和年龄都通过验证，则输出“注册成功！”，如下所示。

```
------------用户注册--------------
用户名：
admin
密码：
admin
年龄：
123
注册成功！
```