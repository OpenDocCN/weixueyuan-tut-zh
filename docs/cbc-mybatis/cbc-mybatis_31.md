# MyBatis 动态 sql 之 if 标签：条件判断

> 原文：[`c.biancheng.net/view/4375.html`](http://c.biancheng.net/view/4375.html)

开发人员通常根据需求手动拼接 SQL 语句，这是一个极其麻烦的工作，而 MyBatis 提供了对 SQL 语句动态组装的功能，恰能解决这一问题。

MyBatis 的动态 SQL 元素与 JSTL 或 XML 文本处理器相似，常用 <if>、<choose>、<when>、<otherwise>、<trim>、<where>、<set>、<foreach> 和 <bind> 等元素。

创建 myBatisDemo03 应用，并将《MyBatis 与 Spring 的整合实例详解》的 MyBatis-Spring 应用的所有 JAR 包和 src 中所有 Java 程序与 XML 文件都复制到 myBatisDemo03 的相应位置。

动态 SQL 通常要做的事情是有条件地包含 where 子句的一部分，所以在 MyBatis 中 <if> 元素是最常用的元素，它类似于 Java 中的 if 语句。在 myBatisDemo03 应用中测试 <if> 元素，具体过程如下：

#### 1）添加 SQL 映射语句

在 com.mybatis 包的 UserMapper.xml 文件中添加如下 SQL 映射语句：

```

<!--使用 if 元素根据条件动态查询用户信息-->
<select id="selectUserByIf" resultType="com.po.MyUser" parameterType="com.po.MyUser">
    select * from user where 1=1
    <if test="uname!=null and uname!=''">
        and uname like concat('%',#{uname},'%')
    </if >
    <if test="usex !=null and usex !=''">
        and usex=#{usex}
    </if >
</select>
```

#### 2）添加数据操作接口方法

在 com.dao 包的 UserDao 接口中添加如下数据操作接口方法：

public List<MyUser> selectUserByIf(MyUser user);

#### 3）调用数据操作接口方法

在 com.controller 包的 UserController 类中添加如下程序调用数据操作接口方法。

```

// 使用 if 元素查询用户信息
MyUser ifmu=new MyUser();
ifmu.setUname ("张");
ifmu.setUsex ("女");
List<MyUser> listByif=userDao.selectUserByIf(ifmu);
System.out.println ("if 元素================");
for (MyUser myUser:listByif) {
    System.out.println(myUser);
}
```

#### 4）测试动态 SQL 语句

运行 com.controller 包中的 TestController 主类，测试动态 SQL 语句。