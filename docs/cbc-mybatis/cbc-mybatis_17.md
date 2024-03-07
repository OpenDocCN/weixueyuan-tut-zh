# MyBatis 自定义 TypeHandler

> 原文：[`c.biancheng.net/view/4341.html`](http://c.biancheng.net/view/4341.html)

在大部分的场景下，MyBatis 的 typeHandler 就能应付一般的场景，但是有时候不够用。比如使用枚举的时候，枚举有特殊的转化规则，这个时候需要自定义 typeHandler 进行处理它。

从系统定义的 typeHandler 可以知道，要实现 typeHandler 就需要去实现接口 typeHandler，或者继承 BaseTypeHandler（实际上，BaseTypeHandler 实现了 typeHandler 接口）。

这里我们仿造一个 StringTypeHandler 来实现一个自定义的 typeHandler——MyTypeHandler，它只是用于实现接口 typeHandler，如下所示。

```

package com.mybatis.test;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.SQLException;

import java.sql.ResultSet;

import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.TypeHandler;
import org.apache.log4j.Logger;

public class MyTypeHandler implements TypeHandler<String> {
    Logger logger = Logger.getLogger(MyTypeHandler.class);

    @Override
    public void setParameter(PreparedStatement ps, int i, String parameter,
            JdbcType jdbcType) throws SQLException {
        logger.info("设置 string 参数【" + parameter + "】");
        ps.setString(i, parameter);
    }

    @Override
    public String getResult(ResultSet rs, String columnName)
            throws SQLException {
        String result = rs.getString(columnName);
        logger.info("读取 string 参数 1 【" + result + "】");
        return result;
    }

    @Override
    public String getResult(ResultSet rs, int columnIndex) throws SQLException {
        String result = rs.getString(columnIndex);
        logger.info("读取 string 参数 2【" + result + "】");
        return result;
    }

    @Override
    public String getResult(CallableStatement cs, int columnIndex)
            throws SQLException {
        String result = cs.getString(columnIndex);
        logger.info("读取 string 参数 3 【" + result + "】");
        return result;
    }
}
```

定义的 typeHandler 泛型为 String，显然我们要把数据库的数据类型转化为 String 型，然后实现设置参数和获取结果集的方法。但是这个时候还没有启用 typeHandler，它还需要做如下所示的配置。

<typeHandlers>
    <typeHandler jdbcType="VARCHAR" javaType="string" handler="com.mybatis.test.MyTypeHandler"/>
</typeHandlers>

配置完成后系统才会读取它，这样注册后，当 jdbcType 和 javaType 能与 MyTypeHandler 对应的时候，它就会启动 MyTypeHandler。有时候还可以显式启用 typeHandler，一般而言启用这个 typeHandler 有两种方式，如下所示。

```

....
<resultMap id="roleMapper" type="role">
    <result property="id" column="id"/>
    <result property="roleName" column="role_name" jdbcType="VARCHAR" javaType="string"/>
    <result property="note" column="note" typeHandler=" com.mybatis.test.MyTypeHandler"/>
</resultMap>

<select id="getRole" parameterType="long" resultMap="roleMapper">
    select id,role_name,note from t_role where id = #{id}
</select>

<select id="findRoles" parameterType="string" resultMap="roleMapper">
    select id, role_name, note from t_role
    where role_name like concat('%',#{roleName, jdbcType=VARCHAR,
    javaType=string}, '%')
</select>
<select id="findRoles2" parameterType="string" resultMap="roleMapper">
    select id, role_name, note from t_role
    where note like concat ('%', # {note, typeHandler=com.mybatis.test.MyTypeHandler},'%')
</select>
......
```

注意，要么指定了与自定义 typeHandler 一致的 jdbcType 和 javaType，要么直接使用 typeHandler 指定具体的实现类。

在一些因为数据库返回为空导致无法断定采用哪个 typeHandler 来处理，而又没有注册对应的 javaType 的 typeHandler 时，MyBatis 无法知道使用哪个 typeHandler 转换数据，我们可以采用这样的方式来确定采用哪个 typeHandler 处理，这样就不会有异常出现了。

有时候由于枚举类型很多，系统需要的 typeHandler 也会很多，如果采用配置也会很麻烦，这个时候可以考虑使用包扫描的形式，那么就需要按照以下代码配置了。

<typeHandlertype>
    <package name="com.mybatis.test"/>
</typeHandlertype>

只是这样就没法指定 jdbcType 和 javaType 了，不过我们可以使用注解来处理它们。我们把 MyTypeHandler 的声明修改一下，如下所示。

@MappedTypes(String.class)
@MappedjdbcTypes(jdbcType.VARCHAR)
public class MyTypeHandler implements TypeHandler<String>{
    ......
}