# MyBatis 核心配置文件 properties 元素

> 原文：[`c.biancheng.net/view/4323.html`](http://c.biancheng.net/view/4323.html)

properties 属性可以给系统配置一些运行参数，可以放在 XML 文件或者 properties 文件中，而不是放在 Java 编码中，这样的好处在于方便参数修改，而不会引起代码的重新编译。一般而言，MyBatis 提供了 3 种方式让我们使用 properties，它们是：

*   property 子元素。
*   properties 文件。
*   程序代码传递。

## property 子元素

以下面代码为基础，使用 property 子元素将数据库连接的相关配置进行改写，如下所示。

```

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties>
        <property name="driver" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8" />
        <property name="username" value="root" />
        <property name="password" value="1128" />
    </properties>
    <typeAliases>
        <typeAlias alias="role" type="com.mybatis.po.Role"/>
    </typeAliases>
    <!--数据库环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url"    value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8" />
                <property name="username" value="root" />
                <property name="password" value="1128" />
            </dataSource>
        </environment>
    </environments>
    <!-- 映射文件 -->
    <mappers>
        <mapper resource="com/mybatis/mapper/RoleMapper.xml" />
    </mappers>
</configuration>
```

这里使用了元素 <properties> 下的子元素 <property> 定义，用字符串 database.username 定义数据库用户名，然后就可以在数据库定义中引入这个已经定义好的属性参数，如 ${database.username}，这样定义一次就可以到处引用了。但是如果属性参数有成百上千个，显然使用这样的方式不是一个很好的选择，这个时候可以使用 properties 文件。

## 使用 properties 文件

使用 properties 文件是比较普遍的方法，一方面这个文件十分简单，其逻辑就是键值对应，我们可以配置多个键值放在一个 properties 文件中，也可以把多个键值放到多个 properties 文件中，这些都是允许的，它方便日后维护和修改。

我们创建一个文件 jdbc.properties 放到 classpath 的路径下，如下所示。

database.driver=com.mysql.jdbc.Driver
database.url=jdbc:mysql://localhost:3306/mybatis
database.username=root
database.password=1128

在 MyBatis 中通过 <properties> 的属性 resource 来引入 properties 文件。

<properties resource="jdbc.properties"/>

也可以按 ${database.username} 的方法引入 properties 文件的属性参数到 MyBatis 配置文件中。这个时候通过维护 properties 文件就可以维护我们的配置内容了。

## 使用程序传递方式传递参数

在真实的生产环境中，数据库的用户密码是对开发人员和其他人员保密的。运维人员为了保密，一般都需要把用户和密码经过加密成为密文后，配置到 properties 文件中。

对于开发人员及其他人员而言，就不知道其真实的用户密码了，数据库也不可能使用已经加密的字符串去连接，此时往往需要通过解密才能得到真实的用户和密码了。

现在假设系统已经为提供了这样的一个 CodeUtils.decode（str）进行解密，那么我们在创建 SqlSessionFactory 前，就需要把用户名和密码解密，然后把解密后的字符串重置到 properties 属性中，如下所示。

```

String resource = "mybatis-config.xml";
InputStream inputStream;
Inputstream in = Resources.getResourceAsStream("jdbc.properties");
Properties props = new Properties();
props.load(in);
String username = props.getProperty("database.username");
String password = props.getProperty("database.password");
//解密用户和密码，并在属性中重置
props.put("database.username", CodeUtils.decode(username));
props.put ("database.password", CodeUtils.decode(password)); 
inputstream = Resources.getResourceAsStream(resource);
//使用程序传递的方式覆盖原有的 properties 属性参数
SqlSessionFactory = new SqlSessionFactoryBuilder().build(inputstream, props);
```

首先使用 Resources 对象读取了一个 jdbc.properties 配置文件，然后获取了它原来配置的用户和密码，进行解密并重置，最后使用 SqlSessionFactoryBuilder 的 build 方法，传递多个 properties 参数来完成。

这将覆盖之前配置的密文，这样就能连接数据库了，同时也满足了运维人员对数据库用户和密码安全的要求。