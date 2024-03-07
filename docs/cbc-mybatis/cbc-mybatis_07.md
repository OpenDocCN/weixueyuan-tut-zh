# MyBatis 实现映射器的 2 种方式：XML 文件形式和注解形式

> 原文：[`c.biancheng.net/view/4319.html`](http://c.biancheng.net/view/4319.html)

映射器是 MyBatis 中最重要、最复杂的组件，它由一个接口和对应的 XML 文件（或注解）组成。它可以配置以下内容：

*   描述映射规则。
*   提供 SQL 语句，并可以配置 SQL 参数类型、返回类型、缓存刷新等信息。
*   配置缓存。
*   提供动态 SQL。

本节阐述两种实现映射器的方式，XML 文件形式和注解形式。不过在此之前，先用以下 SQL 语句创建 role 表。

```

CREATE TABLE `role` (
    `id` bigint(20) NOT NULL,
    `role_name` varchar(20) DEFAULT NULL,
    `note` varchar(20) DEFAULT NULL,
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

并且定义一个 POJO，它十分简单，如下所示。

```

package com.mybatis.po;

public class Role {
    private Long id;
    private String roleName;
    private String note;
/**setter and getter**/
}
```

映射器的主要作用就是将 SQL 查询到的结果映射为一个 POJO，或者将 POJO 的数据插入到数据库中，并定义一些关于缓存等的重要内容。

注意，开发只是一个接口，而不是一个实现类。初学者可能会产生一个很大的疑问，那就是接口不是不能运行吗？

是的，接口不能直接运行。MyBatis 运用了动态代理技术使得接口能运行起来，入门阶段只要懂得 MyBatis 会为这个接口生成一个代理对象，代理对象会去处理相关的逻辑即可。

## 用 XML 实现映射器

用 XML 定义映射器分为两个部分：接口和 XML。先定义一个映射器接口，如下所示。

```

package com.mybatis.mapper;
import com.mybatis.po.Role;
public interface RoleMapper {
    public Role getRole(Long id);
}
```

在用 XML 方式创建 SqlSession 的配置文件中有这样一段代码：

<mapper resource="com/mybatis/mapper/RoleMapper.xml" />

它的作用就是引入一个 XML 文件。用 XML 方式创建映射器，如下所示。

```

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis.mapper.RoleMapper">
    <select id="getRole" parameterType="long" resultType="role">
        SELECT id,role_name as roleName,note FROM role WHERE id =#{id}
    </select>
</mapper>
```

有了这两个文件，就完成了一个映射器的定义。XML 文件还算比较简单，我们稍微讲解一下：

<mapper> 元素中的属性 namespace 所对应的是一个接口的全限定名，于是 MyBatis 上下文就可以通过它找到对应的接口。

<select> 元素表明这是一条查询语句，而属性 id 标识了这条 SQL，属性 parameterType="long" 说明传递给 SQL 的是一个 long 型的参数，而 resultType="role" 表示返回的是一个 role 类型的返回值。而 role 是之前配置文件 mybatis-config.xml 配置的别名，指代的是 com.mybatis.po.Role。

这条 SQL 中的 #{id} 表示传递进去的参数。

注意，我们并没有配置 SQL 执行后和 role 的对应关系，它是如何映射的呢？

其实这里采用的是一种被称为自动映射的功能，MyBatis 在默认情况下提供自动映射，只要 SQL 返回的列名能和 POJO 对应起来即可。

这里 SQL 返回的列名 id 和 note 是可以和之前定义的 POJO 的属性对应起来的，而表里的列 role_name 通过 SQL 别名的改写，使其成为 roleName，也是和 POJO 对应起来的，所以此时 MyBatis 就可以把 SQL 查询的结果通过自动映射的功能映射成为一个 POJO。

## 注解实现映射器

除 XML 方式定义映射器外，还可以采用注解方式定义映射器，它只需要一个接口就可以通过 MyBatis 的注解来注入 SQL，如下所示。

```

package com.mybatis.mapper;
import org.apache.ibatis.annotations.Select;
import com.mybatis.po.Role;
public interface RoleMapper2 {
    @Select("select id,role_name as roleName,note from t_role where id=#{id}")
    public Role getRole(Long id);
}
```

这完全等同于 XML 方式创建映射器。也许你会觉得使用注解的方式比 XML 方式要简单得多。如果它和 XML 方式同时定义时，XML 方式将覆盖掉注解方式，所以 MyBatis 官方推荐使用的是 XML 方式，因此本教程以 XML 方式为主讨论 MyBatis 的应用。

在工作和学习中，SQL 的复杂度远远超过我们现在看到的 SQL，比如下面这条 SQL。

```

select * from t_user u
left join t_user_role ur on u.id = ur.user_id
left join t_role r on ur.role_id = r.id
left join t_user_info ui on u.id = ui.user_id
left join t_female_health fh on u.id = fh.user_id
left join t_male_health mh on u.id = mh.user_id
where u.user_name like concat('%', ${userName},'%')
and r.role_name like concat('%', ${roleName},'%')
and u.sex = 1
and ui.head_image is not null;
```

显然这条 SQL 比较复杂，如果放入 @Select 中会明显增加注解的内容。如果把大量的 SQL 放入 Java 代码中，显然代码的可读性也会下降。

如果同时还要考虑使用动态 SQL，比如当参数 userName 为空，则不使用 u.user_name like concat（'%',${userName},'%'）作为查询条件；当 roleName 为空，则不使用 r.role_name like concat（'%',${roleName},'%'）作为查询条件，但是还需要加入其他的逻辑，这样就使得这个注解更加复杂了，不利于日后的维护和修改。

此外，XML 可以相互引入，而注解是不可以的，所以在一些比较复杂的场景下，使用 XML 方式会更加灵活和方便。所以大部分的企业都是以 XML 为主，本教程也会保持一致，以 XML 方式来创建映射器。当然在一些简单的表和应用中使用注解方式也会比较简单。

这个接口可以在 XML 中定义，我们仿造在 mybatis-config.xml 中配置 XML 语句：

<mapper resource="com/mybatis/mapper/RoleMapper.xml" />

把它修改为下面的形式即可。

<mapper resource="com/mybatis/mapper/RoleMapper2" />

也可以使用 configuration 对象注册这个接口，比如：

configuration.addMapper(RoleMapper2.class);