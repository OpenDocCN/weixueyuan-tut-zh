# MyBatis 动态 sql 之 trim、where、set 标签

> 原文：[`c.biancheng.net/view/4378.html`](http://c.biancheng.net/view/4378.html)

本节主要讲解 MyBatis 动态 Sql 的 trim、where、set 标签。

## <trim>元素

<trim> 元素的主要功能是可以在自己包含的内容前加上某些前缀，也可以在其后加上某些后缀，与之对应的属性是 prefix 和 suffix。

可以把包含内容的首部某些内容覆盖，即忽略，也可以把尾部的某些内容覆盖，对应的属性是 prefixOverrides 和 suffixOverrides。正因为 <trim> 元素有这样的功能，所以也可以非常简单地利用 <trim> 来代替 <where> 元素的功能。

在 myBatisDemo03 应用中测试 <trim> 元素，具体过程如下：

#### 1）添加 SQL 映射语句

在 com.mybatis 包的 UserMapper.xml 文件中添加如下 SQL 映射语句：

```

<!--使用 trim 元素根据条件动态查询用户信息-->
<select id="selectUserByTrim" resultType="com.po.MyUser"parameterType="com.po.MyUser">
    select * from user
    <trim prefix="where" prefixOverrides = "and | or">
        <if test="uname!=null and uname!=''">
            and uname like concat('%',#{uname},'%')
        </if>
        <if test="usex!=null and usex!=''">
            and usex=#{usex}
        </if>
    </trim>
</select>
```

#### 2）添加数据操作接口方法

在 com.dao 包的 UserDao 接口中添加如下数据操作接口方法：

public List<MyUser> selectUserByTrim(MyUser user);

#### 3）调用数据操作接口方法

在 com.controller 包的 UserController 类中添加如下程序调用数据操作接口方法。

```

// 使用 trim 元素查询用户信息
MyUser trimmu=new MyUser();
trimmu.setUname ("张");
trimmu.setUsex("男");
List<MyUser> listByTrim=userDao.selectUserByTrim(trimmu);
System.out.println ("trim 元素=========================");
for (MyUser myUser:listByTrim) {
    System.out.println(myUser);
}
```

#### 4）测试动态 SQL 语句

运行 com.controller 包中的 TestController 主类，测试动态 SQL 语句。

## <where> 元素

<where> 元素的作用是会在写入 <where> 元素的地方输出一个 where 语句，另外一个好处是不需要考虑 <where> 元素里面的条件输出是什么样子的，MyBatis 将智能处理。如果所有的条件都不满足，那么 MyBatis 就会查出所有的记录，如果输出后是以 and 开头的，MyBatis 会把第一个 and 忽略。

当然如果是以 or 开头的，MyBatis 也会把它忽略；此外，在 <where> 元素中不需要考虑空格的问题，MyBatis 将智能加上。

在 myBatisDemo03 应用中测试 <where> 元素，具体过程如下：

#### 1）添加 SQL 映射语句

在 com.mybatis 包的 UserMapper.xml 文件中添加如下 SQL 映射语句：

```

<!--使用 where 元素根据条件动态查询用户信息-->
<select id="selectUserByWhere" resultType="com.po.MyUser" parameterType="com.po.MyUser">
    select * from user
    <where>
        <if test="uname != null and uname ! = ''">
            and uname like concat('%',#{uname},'%')
        </if>
        <if test="usex != null and usex != '' ">
            and usex=#{usex}
        </if >
    </where>
</select>
```

#### 2）添加数据操作接口方法

在 com.dao 包的 UserDao 接口中添加如下数据操作接口方法：

public List<MyUser> selectUserByWhere(MyUser user);

#### 3）调用数据操作接口方法

在 com.controller 包的 UserController 类中添加如下程序调用数据操作接口方法。

```

// 使用 where 元素查询用户信息
MyUser wheremu=new MyUser();
wheremu.setUname ("张");
wheremu.setUsex("男");
List<MyUser> listByWhere=userDao.selectUserByWhere(wheremu);
System.out.println ("where 元素=========================");
for (MyUser myUser:listByWhere) {
    System.out.println(myUser);
}
```

#### 4）测试动态 SQL 语句

运行 com.controller 包中的 TestController 主类，测试动态 SQL 语句。

## <set>元素

在动态 update 语句中可以使用 <set> 元素动态更新列。在 myBatisDemo03 应用中测试 <set> 元素，具体过程如下：

#### 1）添加 SQL 映射语句

在 com.mybatis 包的 UserMapper.xml 文件中添加如下 SQL 映射语句：

```

<!--使用 set 元素动态修改一个用户-->
<update id="updateUserBySet" parameterType="com.po.MyUser">     
    update user
    <set>
        <if test="uname!=null">uname=#{uname}</if>
        <if test="usex!=null">usex=#{usex}</if>
    </set>
    where uid=#{uid}
</update>
```

#### 2）添加数据操作接口方法

在 com.dao 包的 UserDao 接口中添加如下数据操作接口方法：

public int updateUserBySet(MyUser user);

#### 3）调用数据操作接口方法

在 com.controller 包的 UserController 类中添加如下程序调用数据操作接口方法。

```

// 使用 set 元素查询用户信息
MyUser setmu=new MyUser();
setmu.setUid (1);
setmu.setUname("张九");
int setup=userDao.updateUserBySet(setmu);
System.out.println ("set 元素修改了"+setup+"条记录");
System.out.println ("=========================")
```

#### 4）测试动态 SQL 语句

运行 com.controller 包中的 TestController 主类，测试动态 SQL 语句。