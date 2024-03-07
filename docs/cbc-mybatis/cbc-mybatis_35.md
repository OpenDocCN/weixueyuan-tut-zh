# MyBatis 动态 sql 之 bind 标签

> 原文：[`c.biancheng.net/view/4382.html`](http://c.biancheng.net/view/4382.html)

在进行模糊查询时，如果使用“${}”拼接字符串，则无法防止 SQL 注入问题。如果使用字符串拼接函数或连接符号，但不同数据库的拼接函数或连接符号不同。

例如 MySQL 的 concat 函数、Oracle 的连接符号“||”，这样 SQL 映射文件就需要根据不同的数据库提供不同的实现，显然比较麻烦，且不利于代码的移植。幸运的是，MyBatis 提供了 <bind> 元素来解决这一问题。

在 myBatisDemo03 应用中测试 <bind> 元素，具体过程如下： 

#### 1）添加 SQL 映射语句

在 com.mybatis 包的 UserMapper.xml 文件中添加如下 SQL 映射语句：

```

<!--使用 bind 元素进行模糊查询-->
<select id="selectUserByBind" resultType="com.po.MyUser" parameterType= "com.po.MyUser">
    <!-- bind 中的 uname 是 com.po.MyUser 的属性名-->
    <bind name="paran_uname" value="'%' + uname + '%'"/>
        select * from user where uname like #{paran_uname}
</select>
```

#### 2）添加数据操作接口方法

在 com.dao 包的 UserDao 接口中添加如下数据操作接口方法：

public List<MyUser> selectUserByBind(MyUser user);

#### 3）调用数据操作接口方法

在 com.controller 包的 UserController 类中添加如下程序调用数据操作接口方法。

```

// 使用 bind 元素查询用户信息
MyUser bindmu=new MyUser();
bindmu.setUname ("张");
List<MyUser> listByBind=userDao.selectUserByBind(bindmu);
System.out.println ("bind 元素=========================");
for (MyUser myUser:listByBind) {
    System.out.println(myUser);
}
```

#### 4）测试动态 SQL 语句

运行 com.controller 包中的 TestController 主类，测试动态 SQL 语句。