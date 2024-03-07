# MyBatis resultMap 元素的结构及使用

> 原文：[`c.biancheng.net/view/4366.html`](http://c.biancheng.net/view/4366.html)

<resultMap> 元素表示结果映射集，是 MyBatis 中最重要也是最强大的元素，主要用来定义映射规则、级联的更新以及定义类型转化器等。

## <resultMap> 元素的结构

<resultMap> 元素包含了一些子元素，结构如下：

```

<resultMap id="" type="">
    <constructor><!-- 类再实例化时用来注入结果到构造方法 -->
        <idArg/><!-- ID 参数，结果为 ID -->
        <arg/><!-- 注入到构造方法的一个普通结果 -->  
    </constructor>
    <id/><!-- 用于表示哪个列是主键 -->
    <result/><!-- 注入到字段或 JavaBean 属性的普通结果 -->
    <association property=""/><!-- 用于一对一关联 -->
    <collection property=""/><!-- 用于一对多、多对多关联 -->
    <discriminator javaType=""><!-- 使用结果值来决定使用哪个结果映射 -->
        <case value=""/><!-- 基于某些值的结果映射 -->
    </discriminator>
</resultMap>
```

*   <resultMap> 元素的 type 属性表示需要的 POJO，id 属性是 resultMap 的唯一标识。
*   子元素 <constructor> 用于配置构造方法（当 POJO 未定义无参数的构造方法时使用）。
*   子元素 <id> 用于表示哪个列是主键。
*   子元素 <result> 用于表示 POJO 和数据表普通列的映射关系。
*   子元素 <association>、<collection> 和 <discriminator> 用在级联的情况下。关于级联的问题比较复杂，后面教程会详细讲解。

一条查询 SQL 语句执行后将返回结果，而结果可以使用 Map 存储，也可以使用 POJO 存储。

## 使用 Map 存储结果集

任何 select 语句都可以使用 Map 存储结果，示例代码如下：

```

<!-- 查询所有用户信息存到 Map 中 -->
<select id="selectAllUserMap" resultType="map">
    select * from user
</select>
```

测试上述 SQL 配置文件的过程如下：

首先在 com.dao.UserDao 接口中添加以下接口方法。

public List<Map<String,Object>> selectAllUserMap();

然后在 com.controller 包的 UserController 类中调用接口方法，具体代码如下。

```

// 查询所有用户信息存到 Map 中
List<Map<String, Object>> lmp = userDao.selectAllUserMap();
for (Map<String, Object> map : lmp) {
    System.out.println(map);
}
```

上述 Map 的 key 是 select 语句查询的字段名（必须完全一样），而 Map 的 value 是查询返回结果中字段对应的值，一条记录映射到一个 Map 对象中。Map 用起来很方便，但可读性稍差，有的开发者不太喜欢使用 Map，更多时候喜欢使用 POJO 的方式。

## 使用 POJO 存储结果集

有的开发者喜欢使用 POJO 的方式存储结果集，一方面可以使用自动映射，例如使用 resultType 属性，但有时候需要更为复杂的映射或级联，这时候就需要使用 <select> 元素的 resultMap 属性配置映射集合。具体步骤如下：

#### 1）创建 POJO 类

在 myBatisDemo02 应用的 com.pojo 包中创建 POJO 类 MapUser。MapUser 类的代码如下：

```

package com.pojo;

public class MapUser {
    private Integer m_uid;
    private String m_uname;
    private String m_usex;
    // 此处省略 setter 和 getter 方法
    @Override
    public String toString() {
        return "User[uid=" + m_uid + ",uname=" + m_uname + ",usex=" + m_usex
                + "]";
    }
}
```

#### 2）配置 <resultMap> 元素

在 SQL 映射文件 UserMapper.xml 中配置 <resultMap> 元素，其属性 type 引用 POJO 类。具体配置如下：

```

<!--使用自定义结果集类型-->
<resultMap type="com.pojo.MapUser" id="myResult">
    <!-- property 是 com.pojo.MapUser 类中的属性-->
    <!-- column 是查询结果的列名，可以来自不同的表-->
    <id property="m_uid" column="uid"/>
    <result property="m_uname" column="uname"/>
    <result property="m_usex" column="usex"/>
</resultMap>
```

#### 3）配置<select>元素

在 SQL 映射文件 UserMapper.xml 中配置 <select> 元素，其属性 resultMap 引用了 <resultMap> 元素的 id。具体配置如下：

```

<!-- 使用自定义结果集类型查询所有用户 -->
<select id="selectResultMap" resultMap="myResult">
    select * from user
</select>
```

#### 4）添加接口方法

在 com.dao.UserDao 接口中添加以下接口方法：

public List<MapUser> selectResultMap();

#### 5）调用接口方法

在 com.controller 包的 UserController 类中调用接口方法，具体代码如下：

```

// 使用 resultMap 映射结果集
List<MapUser> listResultMap = userDao.selectResultMap();
for (MapUser myUser : listResultMap) {
    System.out.println(myUser);
}
```