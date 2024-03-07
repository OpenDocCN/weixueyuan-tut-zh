# MyBatis BlobTypeHandler 读取 Blob 类型字段

> 原文：[`c.biancheng.net/view/4344.html`](http://c.biancheng.net/view/4344.html)

MyBatis 对数据库的 Blob 字段也进行了支持，它提供了一个 BlobTypeHandler，为了应付更多的场景，它还提供了 ByteArrayTypeHandler，只是它不太常用，这里为读者展示 BlobTypeHandler 的使用方法。首先建一个表。

```

create table file(
    id int(12) not null auto_increment,
    content blob not null,
    primary key(id)
);
```

加粗的代码，使用了 Blob 字段，用于存入文件。然后创建一个 POJO，用于处理这个表，如下所示。

public class TestFile{
    long id;
    byte[] content;
    /** setter and getter **/
}

这里需要把 content 属性和数据库的 Blob 字段转换，这个时候可以使用系统注册的 typeHandler——BlobTypeHandler 来转换，如下所示。

```

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ssm.chapter5.mapper.FileMapper">
    <resultMap type="com.ssm.chapter5.pojo.TestFile" id="file">
        <id column="id" property="id"/>
        <id column="content" property="content" typeHandler="org.apache.ibatis.type.BlobTypeHandler"/>
    </resultMap>

    <select id="getFile" parameterType="long" resultMap="file"> 
        select id, content from t_file where id = #{id}
    </select>

    <insert id="insertFile" parameterType="com.ssm.chapter5.pojo.TestFile">
        insert into t_file(content) values(#{content})
    </insert>
</mapper>
```

实际上，不加入加粗代码的 typeHandler 属性，MyBatis 也能检测得到，并使用合适的 typeHandler 进行转换。

在现实中，一次性地将大量数据加载到 JVM 中，会给服务器带来很大压力，所以在更多的时候，应该考虑使用文件流的形式。这个时候只要把 POJO 的属性 content 修改为 InputStream 即可。

如果没有 typeHandler 声明，那么系统就会探测并使用 BlobInputStream TypeHandler 为你转换结果，这个时候需要把加粗代码的 typeHandler 修改为 org.apache.ibatis.type.BlobInputStreamTypeHandler。

因为性能不佳，文件的操作在大型互联网的网站上并不常用。更多的时候，大型互联网的网站会采用文件服务器的形式，通过更为高速的文件系统操作。这是搭建高效服务器需要注意的地方。