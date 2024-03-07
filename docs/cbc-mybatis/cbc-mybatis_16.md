# MyBatis 系统定义的 TypeHandler

> 原文：[`c.biancheng.net/view/4339.html`](http://c.biancheng.net/view/4339.html)

MyBatis 内部定义了许多有用的 typeHandler，如表 1 所示。

表 1 系统定义的 typeHandler

| 类型处理器 | Java 类型 | JDBC 类型 |
| BooleanTypeHandler | java.lang.Boolean，boolean | 数据库兼容的 BOOLEAN |
| ByteTypeHandler | java.lang.Byte，byte | 数据库兼容的 NUMERIC 或 BYTE |
| ShortTypeHandler | java.lang.Short，short | 数据库兼容的 NUMERIC 或 SHORT INTEGER |
| IntegerTypeHandler | java.lang.Integer，int | 数据库兼容的 NUMERIC 或 INTEGER |
| LongTypeHandler | java.lang.Long，long | 数据库兼容的 NUMERIC 或 LONG INTEGER |
| FloatTypeHandler | java.lang.Float，float | 数据库兼容的 NUMERIC 或 FLOAT |
| DoubleTypeHandler | java.lang.Double，double | 数据库兼容的 NUMERIC 或 DOUBLE |
| BigDecimalTypeHandler | java.math.BigDecimal | 数据库兼容的 NUMERIC 或 DECIMAL |
| StringTypeHandler | java.lang.String | CHAR、VARCHAR |
| ClobReaderTypeHandler | java.io.Reader | —— |
| ClobTypeHandler | java.lang.String | CLOB、LONGVARCHAR |
| NStringTypeHandler | java.lang.String | NVARCHAR、NCHAR |
| NClobTypcHandler | java.lang.String | NCLOB |
| BlobInputStreamTypeHandler | java.io.InputStream | —— |
| ByteArrayTypeHandler | byte[] | 数据库兼容的字节流类型 |
| BlobTypeHandler | byte[] | BLOB、LONGVARBINARY |
| DateTypeHandler | java.util.Date | TIMESTAMP |
| DateOnlyTypeHandler | java.util.Date | DATE |
| TimeOnlyTypeHandler | java.util.Date | TIME |
| SqlTimestampTypeHandler | java.sql.Timestamp | TIMESTAMP |
| SqlDateTypeHandler  | java.sql.Date  | DATE |
| SqlTimeTypeHandler | java.sql.Time | TIME |
| ObjectTypeHandler | Any | OTHER 或未指定类型 |
| EnumTypeHandler | Enumeration Type | VARCHAR 任何兼容的字符串类型，存储枚举的名称（而不是索引） |
| EnumOrdinalTypeHandler | Enumeration Type | 任何兼容的 NUMERIC 或 DOUBLE 类型，存储枚举的索引（而不是名称） |

这些就是 MyBatis 系统已经创建好的 typeHandler。在大部分的情况下无须显式地声明 jdbcType 和 javaType，或者用 typeHandler 去指定对应的 typeHandler 来实现数据类型转换，因为 MyBatis 系统会自己探测。有时候需要修改一些转换规则，比如枚举类往往需要自己去编写规则。

在 MyBatis 中 typeHandler 都要实现接口 org.apache.ibatis.type.TypeHandler，首先让我们先看看这个接口的定义，如下所示。

public interface TypeHandler<T>
    void setParameter(PreparedStatement ps, int i, T parameter, jdbcType jdbcType)
    throws SQLException;
    T getResult(ResultSet rs, String columnName) throws SQLException;
    T getResult(ResultSet rs, int columnIndex) throws SQLException;
    T getResult(CallableStatement cs, int columnIndex) throws SQLException;
}

这里我们稍微说明一下它的定义。

其中 T 是泛型，专指 javaType，比如我们需要 String 的时候，那么实现类可以写为 implements TypeHandler<String>。

setParameter 方法，是使用 typeHandler 通过 PreparedStatement 对象进行设置 SQL 参数的时候使用的具体方法，其中 i 是参数在 SQL 的下标，parameter 是参数，jdbcType 是数据库类型。

其中有 3 个 getResult 的方法，它的作用是从 JDBC 结果集中获取数据进行转换，要么使用列名（columnName）要么使用下标（columnIndex）获取数据库的数据，其中最后一个 getResult 方法是存储过程专用的。

在编写 typeHandler 前，先来研究一下 MyBatis 系统的 typeHandler 是如何实现的，所以有必要先研究一下 MyBatis 系统的 typeHandler。如果读者打开源码，就可以发现它们都继承了 org.apache.ibatis.type.BaseTypeHandler，如下所示。

```

public abstract class BaseTypeHandler<T> extends TypeReference<T> implements TypeHandler<T> {

    protected Configuration configuration;

    public void setConfiguration(Configuration c) {
        this.configuration = c;
    }

    @Override
    public void setParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException {
        if (parameter == null) {
            if (jdbcType == null) {
                throw new TypeException("JDBC requires that the JdbcType must be specified for all nullable parameters.");
            }
            try {
                ps.setNull(i, jdbcType.TYPE_CODE);
            } catch (SQLException e) {
                throw new TypeException("Error setting null for parameter #" + i + " with JdbcType " + jdbcType + " . " + "Try setting a different JdbcType for this parameter or a different jdbcTypeForNull configuration property. " + "Cause: " + e, e);
            }
        } else {
            try {
                setNonNullParameter(ps, i, parameter, jdbcType);
            } catch (Exception e) {
                throw new TypeException("Error setting non null for parameter #" + i + " with JdbcType " + jdbcType + " . " + "Try setting a different JdbcType for this parameter or a different configuration property. " + "Cause: " + e, e);
            }
        }
    }

    @Override
    public T getResult(ResultSet rs, String columnName) throws SQLException {
        T result;
        try {
            result = getNullableResult(rs, columnName);
        } catch (Exception e) {
            throw new ResultMapException("Error attempting to get column '" + columnName + "' from result set.Cause: " + e, e);
        }
        if (rs.wasNull()) {
            return null;
        } else {
            return result;
        }
    }

    @Override
    public T getResult(ResultSet rs, int columnIndex) throws SQLException {
        T result;
        try {
            result = getNullableResult(rs, columnIndex);
        } catch (Exception e) {
            throw new ResultMapException("Error attempting to get column #" + columnIndex+ " from result set.Cause: " + e, e);
        }
        if (rs.wasNull()) {
            return null;
        } else {
            return result;
        }
    }

    @Override
    public T getResult(CallableStatement cs, int columnIndex) throws SQLException {
        T result;
        try {
            result = getNullableResult(cs, columnIndex);
        } catch (Exception e) {
            throw new ResultMapException("Error attempting to get column #" + columnIndex+ " from callable statement.Cause: " + e, e);
        }
        if (cs.wasNull()) {
            return null;
        } else {
            return result;
        }
    }

    public abstract void setNonNullParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException;

    public abstract T getNullableResult(ResultSet rs, String columnName) throws SQLException;

    public abstract T getNullableResult(ResultSet rs, int columnIndex) throws SQLException;

    public abstract T getNullableResult(CallableStatement cs, int columnIndex) throws SQLException;

}
```

简单分析一下 BaseTypeHandler 的源码。

*   BaseTypeHandler 是个抽象类，需要子类去实现其定义的 4 个抽象方法，而它本身实现了 typeHandler 接口的 4 个方法。
*   getResult 方法，非空结果集是通过 getNullableResult 方法获取的。如果判断为空，则返回 null。
*   setParameter 方法，当参数 parameter 和 jdbcType 同时为空时，MyBatis 将抛出异常。如果能明确 jdbcType，则会进行空设置；如果参数不为空，那么它将采用 setNonNullParameter 方法设置参数。
*   getNullableResult 方法用于存储过程。

MyBatis 使用最多的 typeHanlder 之一 —— StringTypeHandler。它用于字符串转换，其源码如下所示。

```

@Override
public void setNonNullParameter(PreparedStatement ps, int i, String parameter, JdbcType jdbcType)
      throws SQLException {
    ps.setString(i, parameter);
}

@Override
public String getNullableResult(ResultSet rs, String columnName)
      throws SQLException {
    return rs.getString(columnName);
}

@Override
public String getNullableResult(ResultSet rs, int columnIndex)
      throws SQLException {
    return rs.getString(columnIndex);
}

@Override
public String getNullableResult(CallableStatement cs, int columnIndex)
      throws SQLException {
    return cs.getString(columnIndex);
}
```

显然它实现了 BaseTypeHandler 的 4 个抽象方法，代码也非常简单。

在这里，MyBatis 把 javaType 和 jdbcType 相互转换，那么它们是如何注册的呢？在 MyBatis 中采用 org.apache.ibatis.type.TypeHandlerRegistry 类对象的 register 方法进行注册，如下所示。 

```

public TypeHandlerRegistry()  {
    register(Boolean.class, new BooleanTypeHandler());
    register(boolean.class, new BooleanTypeHandler());
    ....
    register(byte[].class, jdbcType.BLOB, new BlobTypeHandler());
    register(byte[].class, jdbcType.LONGVARBINARY, new BlobTypeHandler());
    ....
}
```

这样就实现了用代码的形式注册 typeHandler。注意，自定义的 typeHandler 一般不会使用代码注册，而是通过配置或扫描，下一节我们开始学习 MyBatis 自定义 TypeHandler。