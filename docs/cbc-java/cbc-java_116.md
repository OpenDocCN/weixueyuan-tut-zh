# Java 使用自定义包

包的声明和使用非常简单，在了解基本语法之后，下面通过一个案例演示在 Java 程序中声明包，以及不同包之间类的使用。

(1) 创建一个名为 com.dao 的包。

(2) 向 com.dao 包中添加一个 Student 类，该类包含一个返回 String 类型数组的 GetAll() 方法。Student 类代码如下：

```
package com.dao;
public class Student
{
    public static String[] GetAll()
    {
        String[] namelist={"李潘","邓国良","任玲玲","许月月","欧阳娜","赵晓慧"};
        return namelist;
    }
}
```

(3) 创建 com.test 包，在该包里创建带 main() 方法的 Test 类。

(4) 在 main() 方法中遍历 Student 类的 GetAll() 方法中的元素内容，在遍历内容之前，使用 import 引入 com.dao 整个包。完整代码如下：

```
package com.test;
import com.dao.Student;
public class Test
{
    public static void main(String[] args)
    {
        System.out.println("学生信息如下：");
        for(String str:Student.GetAll())
        {
            System.out.println(str);
        }
    }
}
```

(5) 运行上一步骤的代码进行测试，最终的输出结果如下：

```
学生信息如下：
李潘
邓国良
任玲玲
许月月
欧阳娜
赵晓慧
```