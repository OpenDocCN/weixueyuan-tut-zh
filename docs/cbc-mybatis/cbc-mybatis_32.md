# MyBatis 动态 sql 之 choose、when、otherwise 标签

> 原文：[`c.biancheng.net/view/4376.html`](http://c.biancheng.net/view/4376.html)

有些时候不想用到所有的条件语句，而只想从中择取一二，针对这种情况，MyBatis 提供了 <choose> 元素，它有点像 Java 中的 switch 语句。在 myBatisDemo03 应用中测试 <choose> 元素，具体过程如下：

#### 1）添加 SQL 映射语句

在 com.mybatis 包的 UserMapper.xml 文件中添加如下 SQL 映射语句：

```

<!--使用 choose、when、otherwise 元素根据条件动态查询用户信息-->
<select id="selectUserByChoose" resultType="com.po.MyUser" parameterType= "com.po.MyUser">
    select * from user where 1=1
    <choose>
        <when test="uname!=null and uname!=''">
            and uname like concat('%',#{uname},'%')
        </when>
        <when test="usex!=null and usex!=''">
            and usex=#{usex}
        </when>
        <otherwise>
            and uid > 10
        </otherwise>
    </choose>
</select>
```

#### 2）添加数据操作接口方法

在 com.dao 包的 UserDao 接口中添加如下数据操作接口方法：

public List<MyUser> selectUserByChoose(MyUser user);

#### 3）调用数据操作接口方法

在 com.controller 包的 UserController 类中添加如下程序调用数据操作接口方法。

```

// 使用 choose 元素查询用户信息
MyUser choosemu=new MyUser();
choosemu.setUname("");
choosemu.setUsex("");
List<MyUser> listByChoose = UserDao.selectUserEyChoose(choosemu);
System.out.println ("choose 元素================");
for (MyUser myUser:listByChoose) {
    System.out.println(myUser);
}
```

#### 4）测试动态 SQL 语句

运行 com.controller 包中的 TestController 主类，测试动态 SQL 语句。