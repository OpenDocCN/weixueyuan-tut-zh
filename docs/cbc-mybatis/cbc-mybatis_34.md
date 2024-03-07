# MyBatis 动态 sql 之 foreach 标签

> 原文：[`c.biancheng.net/view/4381.html`](http://c.biancheng.net/view/4381.html)

<foreach> 元素主要用在构建 in 条件中，它可以在 SQL 语句中迭代一个集合。

<foreach> 元素的属性主要有 item、index、collection、open、separator、close。

*   item 表示集合中每一个元素进行迭代时的别名。
*   index 指定一个名字，用于表示在迭代过程中每次迭代到的位置。
*   open 表示该语句以什么开始。
*   separator 表示在每次进行迭代之间以什么符号作为分隔符。
*   close 表示以什么结束。

在使用 <foreach> 元素时，最关键、最容易出错的是 collection 属性，该属性是必选的，但在不同情况下该属性的值是不一样的，主要有以下 3 种情况：

*   如果传入的是单参数且参数类型是一个 List，collection 属性值为 list。
*   如果传入的是单参数且参数类型是一个 array 数组，collection 的属性值为 array。
*   如果传入的参数是多个，需要把它们封装成一个 Map，当然单参数也可以封装成 Map。Map 的 key 是参数名，collection 属性值是传入的 List 或 array 对象在自己封装的 Map 中的 key。

在 myBatisDemo03 应用中测试 <foreach> 元素，具体过程如下：

#### 1）添加 SQL 映射语句

在 com.mybatis 包的 UserMapper.xml 文件中添加如下 SQL 映射语句：

```

<!--使用 foreach 元素查询用户信息-->
<select id="selectUserByForeach" resultType="com.po.MyUser" parameterType=
"List">
    select * from user where uid in
    <foreach item="item" index="index" collection="list"
    open="(" separator="," close=")">
        # {item}
    </foreach>
</select>
```

#### 2）添加数据操作接口方法

在 com.dao 包的 UserDao 接口中添加如下数据操作接口方法：

public List<MyUser> selectUserByForeach(List<Integer> listId);

#### 3）调用数据操作接口方法

在 com.controller 包的 UserController 类中添加如下程序调用数据操作接口方法。

```

//使用 foreach 元素查询用户信息
List<Integer> listId=new ArrayList<Integer>();
listId.add(34);
listId.add(37);
List<MyUser> listByForeach = userDao.selectUserByForeach(listId);
System.out.println ("foreach 元素================");
for(MyUser myUser : listByForeach) {
    System.out.println(myUser);
}
```

#### 4）测试动态 SQL 语句

运行 com.controller 包中的 TestController 主类，测试动态 SQL 语句。