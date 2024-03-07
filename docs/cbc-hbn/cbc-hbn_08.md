# Hibernate hibernate.cfg.xml 文件和 C3P0 连接池的配置

> 原文：[`c.biancheng.net/view/4179.html`](http://c.biancheng.net/view/4179.html)

Hibernate 的配置文件包含了数据库连接的相关信息，以及映射文件的基本信息。通常情况下，配置文件默认放在 src 目录下，发布后，该文件会在项目的 WEB-INF/classes 路径下。配置文件的常用配置信息如下所示：

```

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
          "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
          "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- 指定方言 -->
        <property name="dialect">
            org.hibernate.dialect.MySQL5Dialect
        </property>
        <!-- 链接数据库 url -->
        <property name="connection.url">
              <![CDATA[jdbc:mysql://localhost:3306/hibernate?useUnicode=true&characterEncoding=utf-8]]>
        </property>
        <!-- 连接数据库的用户名 -->
        <property name="connection.username">
            root
        </property>
        <!-- 数据库的密码 -->
        <property name="connection.password">
            1128
        </property>
        <!-- 数据库驱动 -->
        <property name="connection.driver_class">
            com.mysql.jdbc.Driver
        </property>
        <!-- 其他配置 -->
        <!-- 显示 sql 语句 -->
        <property name="show_sql">
            true
        </property>
        <!-- 格式化 sql 语句 -->
        <property name="format_sql">true</property>
        <!-- 映射文件配置 -->
        <mapping resource="com/mengma/domain/User.hbm.xml" />
    </session-factory>
</hibernate-configuration>
```

在上述代码中，首先是 xml 声明，然后是配置文件的 dtd 信息，该信息同样可以在核心包 hibernate3.jar 下的 org.hibernate 包中的 hibernate-configuration-3.0.dtd 文件中找到，初学者只需要复制并使用即可。

Hibernate 配置文件的根元素是 <hibernate-configuration>，该元素中包含一个 <session-factory> 子元素，在 <session-factory> 元素中又包含多个 <property> 元素，这些 property 元素用于对 Hibernate 连接数据库的一些信息进行配置。例如，数据库的方言、驱动、URL、用户名、密码等。<mapping> 元素用于加载映射文件的信息。

Hibernate 配置文件的一些常用属性名称及用途如表 1 所示。

表 1 Hibernate 常用配置属性

| 名   称 | 描   述 |
| --- | --- |
| hibernate.dialect | 操作数据库方言 |
| hibernate.connection.driver_class | 连接数据库驱动程序 |
| hibernate.connection.url | 连接数据库 URL |
| hibernate.connection.username | 数据库用户名 |
| hibernate.connection.password | 数据库密码 |
| hibernate.show_sql | 在控制台输出 SQL 语句 |
| hibernate.format_sql | 格式化控制台输出的 SQL 语句 |
| hibernate.hbm2ddl.auto | 当 SessionFactory 创建时是否根据映射文件自动验证表结构或 自动创建、自动更新数据库表结构。该参数的取值为 validate 、update、create 和 create-drop |
| hibernate.connection.autocommit | 事务是否自动提交 |

Hibernate 实现了一种插件结构，它可以集成任何连接池软件。Hibernate 对 C3P0 连接池提供了内嵌支持，所以可以在 Hibernate 中直接配置和使用 C3P0。下面就介绍在 hibernate.cfg.xml 中如何配置和使用 C3P0。

首先，导入 C3P0 的 JAR 包（c3p0-0.9.1.jar）。此 JAR 包可以在已经下载的 hibernate 包的 lib 文件夹下的子目录 optional 中找到。

然后，在 hibernate.cfg.xml 中添加 C3P0 的配置信息，其添加代码如下所示。

```

<!-- C3P0 连接池设定 -->
<!-- 使用 C3P0 连接池配置连接池提供的供应商 -->
<property name="connection.provider_class">
    org.hibernate.connection.c3p0ConnectionProvider
</property>
<!--在连接池中可用的数据库连接的最少数目 -->
<property name="c3p0.min_size">5 </property>
<!--在连接池中所有数据库连接的最大数目 -->
<property name="c3p0.max_sizen">20 </property>
<!--设定数据库连接的过期时间，以 ms 为单位，如果连接池中的某个数据库连接空闲状态的时间 超过 timeout 时间，则会从连接池中清除 -->
<property name="c3p0.timeout">120 </property>
<!--每 3000s 检查所有连接池中的空闲连接以 s 为单位 -->
<property name="c3p0.idle_test_period">3000 </property>
```

在上述配置代码中，首先声明了 C3P0 的供应商信息，然后配置了连接池中的连接最小数目和最大数目等。配置完这些信息后，C3P0 连接池就可以使用了。

最后，通过 JUnit 运行 UserTest 中的 testInsert() 方法，如果执行成功，则表明 C3P0 连接池已经正确配置。