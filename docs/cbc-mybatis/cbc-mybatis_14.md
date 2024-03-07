# MyBatis 配置 typeAliases（别名）详解

> 原文：[`c.biancheng.net/view/4326.html`](http://c.biancheng.net/view/4326.html)

由于类的全限定名称很长，需要大量使用的时候，总写那么长的名称不方便。在 MyBatis 中允许定义一个简写来代表这个类，这就是别名，别名分为系统定义别名和自定义别名。

在 MyBatis 中别名由类 TypeAliasRegistry（org.apache.ibatis.type.TypeAliasRegistry）去定义。注意，在 MyBatis 中别名不区分大小写。

## 系统定义别名

在 MyBatis 的初始化过程中，系统自动初始化了一些别名，如下表所示。

| 别名 | Java 类型 | 是否支持数组 |
| --- | --- | --- |
| _byte | byte | 是 |
| _long | long | 是 |
| _short | short | 是 |
| _int | int | 是 |
| _integer | int | 是 |
| _double | double | 是 |
| _float | float | 是 |
| _boolean | boolean | 是 |
| string | String | 是 |
| byte | Byte | 是 |
| long | Long | 是 |
| short | Short | 是 |
| int | Integer | 是 |
| integer | Integer | 是 |
| double | Double | 是 |
| float | Float | 是 |
| boolean | Boolean | 是 |
| date | Date | 是 |
| decimal | BigDecimal | 是 |
| bigdecimal | BigDecimal | 是 |
| object | Object | 是 |
| map | Map | 否 |
| hashmap | HashMap | 否 |
| list | List | 否 |
| arraylist | ArrayList | 否 |
| collection | Collection | 否 |
| iterator | Iterator | 否 |
| ResultSet | ResultSet | 否 |

如果需要使用对应类型的数组型，要看其是否能支持数据，如果支持只需要使用别名加 `[]` 即可，比如 _int 数组的别名就是 _int[]。而类似 list 这样不支持数组的别名，则不能那么写。

有时候要通过代码来实现注册别名，让我们看看 MyBatis 是如何初始化这些别名的，如下所示。

```

public TypeAliasRegistry() {
    registerAlias("string", String.class);

    registerAlias("byte", Byte.class);
    registerAlias("long", Long.class);
    ......
    registerAlias("byte[]",Byte[].class); registerAlias("long[]",Long[].class);
    ......
    registerAlias("map", Map.class);
    registerAlias("hashmap", HashMap.class);
    registerAlias("list", List.class); registerAlias("arraylist", ArrayList.class);
    registerAlias("collection", Collection.class);
    registerAlias("iterator", Iterator.class);
    registerAlias("ResultSet", ResultSet.class);
}
```

所以使用 TypeAliasRegistry 的 registerAlias 方法就可以注册别名了。一般是通过 Configuration 获取 TypeAliasRegistry 类对象，其中有一个 getTypeAliasRegistry 方法可以获得别名，如 configuration.getTypeAliasRegistry()。

然后就可以通过 registerAlias 方法对别名注册了。而事实上 Configuration 对象也对一些常用的配置项配置了别名，如下所示。

```

//事务方式别名
typeAliasRegistry.registerAlias("JDBC",JdbcTransactionFactory.class);
typeAliasRegistry.registerAlias("MANAGED",ManagedTransactionFactory.class);
//数据源类型别名
typeAliasRegistry.registerAlias("JNDI",JndiDataSourceFactory.class);
typeAliasRegistry.registerAlias("POOLED",
PooledDataSourceFactory.class);
typeAliasRegistry.registerAlias("UNPOOLED",UnpooledDataSourceFactory.class);
//缓存策略别名
typeAliasRegistry.registerAlias("PERPETUAL",PerpetualCache.class);
typeAliasRegistry.registerAlias("FIFO",FifoCache.class);
typeAliasRegistry.registerAlias("LRU",LruCache.class); typeAliasRegistry.registerAlias("SOFT", SoftCache.class); typeAliasRegistry.registerAlias("WEAK", WeakCache.class);
//数据库标识别名
typeAliasRegistry.registerAlias("DB_VENDOR",
VendorDatabaseIdProvider.class);
//语言驱动类别名
typeAliasRegistry.registerAlias("XML",XMLLanguageDriver.class);
typeAliasRegistry.registerAlias("RAW",RawLanguageDriver.class);
//日志类别名
typeAliasRegistry.registerAlias("SLF4J", Slf4jImpl.class);
typeAliasRegistry.registerAlias("COMMONS_LOGGTNG",JakartmCommonsLogginglmpl.class);
typeAliasRegistry.registerAlias("LOG4J", Log4jImpl.class);
typeAliasRegistry.registerAlias("LOG4J2", Log4j2Impl.class);
typeAliasRegistry.registerAlias("JDK_LOGGING", Jdk14LoggingImpl.class);
typeAliasRegistry.registerAlias("STDOUT_LOGGING", StdOutImpl.class);
typeAliasRegistry.registerAlias("NO_LOGGING",NoLoggingImpl.class);
//动态代理别名
typeAliasRegistry.registerAlias("CGLIB",CglibProxyFactory.class);
typeAliasRegistry.registerAlias("JAVASSIST",JavassistProxyFactory.class);
```

这些配置为的是让我们更容易配置 MyBatis 的相关信息。以上就是 MyBatis 系统定义的别名，我们在使用的时候，不要重复命名，导致出现其他问题。

## 自定义别名

由于现实中，特别是大型互联网系统中存在许多对象，比如用户（User）这个对象有时候需要大量重复地使用，因此 MyBatis 也提供了用户自定义别名的规则。我们可以通过 TypeAliasRegistry 类的 registerAlias 方法注册，也可以采用配置文件或者扫描方式来自定义它。

使用配置文件定义很简单：

<typeAliases><!--别名-->
    <typeAlias alias="role" type="com.mybatis.po.Role"/>
    <typeAlias alias="role" type="com.mybatis.po.User"/>
</typeAliases>

这样就可以定义一个别名了。如果有很多类需要定义别名，那么用这样的方式进行配置可就不那么轻松了。MyBatis 还支持扫描别名。比如上面的两个类都在包 com.mybatis.po 之下，那么就可以定义为：

<typeAliases><!--别名-->
    <package name="com.mybatis.po"/>
</typeAliases>

这样 MyBatis 将扫描这个包里面的类，将其第一个字母变为小写作为其别名，比如类 Role 的别名会变为 role，而 User 的别名会变为 user。使用这样的规则，有时候会出现重名。

比如 com.mybatis.po.User 这个类，MyBatis 还增加了对包 com.mybatis.po 的扫描，那么就会出现异常，这个时候可以使用 MyBatis 提供的注解 @Alias（"user3"）进行区分，如下所示。

```

package com.mybatis.po;
@Alias("user3")
public Class User {
    ......
}
```

这样就能够避免因为别名重名导致的扫描失败的问题。