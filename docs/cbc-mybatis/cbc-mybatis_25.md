# MyBatis 中的 insert、update、delete 和 sql 标签

> 原文：[`c.biancheng.net/view/4364.html`](http://c.biancheng.net/view/4364.html)

这节我们来讲 MyBatis 中的 <insert>、<update>、<delete> 和 <sql> 元素。

## <insert>元素

<insert> 元素用于映射插入语句，MyBatis 执行完一条插入语句后将返回一个整数表示其影响的行数。它的属性与 <select> 元素的属性大部分相同，在本节讲解它的几个特有属性。

*   keyProperty：该属性的作用是将插入或更新操作时的返回值赋给 PO 类的某个属性，通常会设置为主键对应的属性。如果是联合主键，可以将多个值用逗号隔开。
*   keyColumn：该属性用于设置第几列是主键，当主键列不是表中的第 1 列时需要设置。如果是联合主键，可以将多个值用逗号隔开。
*   useGeneratedKeys：该属性将使 MyBatis 使用 JDBC 的 getGeneratedKeys（）方法获取由数据库内部产生的主键，例如 MySQL、SQL Server 等自动递增的字段，其默认值为 false。

#### 1）主键（自动递增）回填

MySQL、SQL Server 等数据库的表格可以采用自动递增的字段作为主键，有时可能需要使用这个刚刚产生的主键，用于关联其他业务。

首先为 com.mybatis 包中的 SQL 映射文件 UserMapper.xml 中 id 为 addUser 的 <insert> 元素添加 keyProperty 和 useGeneratedKeys 属性，具体代码如下：

<!--添加一个用户，成功后将主键值返回填给 uid(po 的属性)-->
<insert id="addUser" parameterType="com.po.MyUser" keyProperty="uid" useGeneratedKeys="true">
    insert into user (uname,usex) values(#{uname},#{usex})
</insert>

然后在 com.controller 包的 UserController 类中进行调用，具体代码如下：

```

// 添加一个用户
MyUser addmu = new MyUser();
addmu.setUname("陈恒");
addmu.setUsex("男");
int add = userDao.addUser(addmu);
System.out.println("添加了" + add + "条记录");
System.out.println("添加记录的主键是" + addmu.getUid());
```

#### 2）自定义主键

如果在实际工程中使用的数据库不支持主键自动递增（例如 Oracle），或者取消了主键自动递增的规则，可以使用 MyBatis 的 <selectKey> 元素来自定义生成主键。具体配置示例代码如下：

```

<!-- 添加一个用户，#{uname}为 com.mybatis.po.MyUser 的属性值 -->
<insert id="insertUser" parameterType="com.po.MyUser">
    <!-- 先使用 selectKey 元素定义主键，然后再定义 SQL 语句 -->
    <selectKey keyProperty="uid" resultType="Integer" order="BEFORE">
    select if(max(uid) is null,1,max(uid)+1) as newUid from user)
    </selectKey>
    insert into user (uid,uname,usex) values(#{uid},#{uname},#{usex})
</insert>
```

在执行上述示例代码时，<selectKey> 元素首先被执行，该元素通过自定义的语句设置数据表的主键，然后执行插入语句。

<selectKey> 元素的 keyProperty 属性指定了新生主键值返回给 PO 类（com.po.MyUser）的哪个属性。

*   order 属性可以设置为 BEFORE 或 AFTER。
*   BEFORE 表示先执行 <selectKey> 元素然后执行插入语句。
*   AFTER 表示先执行插入语句再执行 <selectKey> 元素。

## <update>与<delete>元素

<update> 和 <delete> 元素比较简单，它们的属性和 <insert> 元素、<select> 元素的属性差不多，执行后也返回一个整数，表示影响了数据库的记录行数。配置示例代码如下：

```

<!-- 修改一个用户 -->
<update id="updateUser" parameterType="com.po.MyUser">
    update user set uname = #{uname},usex = #{usex} where uid = #{uid}
</update>
<!-- 删除一个用户 -->
<delete id="deleteUser" parameterType="Integer">
    delete from user where uid = #{uid}
</delete>
```

## <sql> 元素

<sql> 元素的作用在于可以定义 SQL 语句的一部分（代码片段），以方便后面的 SQL 语句引用它，例如反复使用的列名。

在 MyBatis 中只需使用 <sql> 元素编写一次便能在其他元素中引用它。配置示例代码如下：

```

<sql id="comColumns">id,uname,usex</sql>
<select id="selectUser" resultType="com.po.MyUser">
    select <include refid="comColumns"> from user
</select>
```

在上述代码中使用 <include> 元素的 refid 属性引用了自定义的代码片段。