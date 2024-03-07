# 填充 CachedRowSet 对象记录集（三种方法）

## 实训内容和要求

运用三种不同的方法填充 CachedRowSet 对象内部的记录集结构（crs 是 CachedRowSet 类的实例对象）。

## 实训步骤

方法一，jlj01.jsp 的代码如下：

```
<%
Class.forName("sun.jdbc.odbc.JDBCodbcDriver");
crs.setURL("jdbc:odbc:test");
crs.setUsername("sa");
crs.setPassword("");
crs.setCommand("SELECT * FROM goods");
crs.execute();
%>
```

方法二，jlj02.jsp 的代码如下：

```
<%
Class.forName("sun.jdbc.odbc.JDBCodbcDriver");
Connection  conn=DriverManager("jdbc:odbc:test","sa","");
crs.setCommand("SELECT * FROM goods");
crs.execute(conn);
%>
```

方法三，jlj03.jsp 的代码如下：

```
<%
Class.forName("sun.jdbc.odbc.JDBCodbcDriver");
Connection  conn=DriverManager("jdbc:odbc:test","sa","");
Statement  stmt=conn.createStatement();
ResultSet rs=stmt.executeQuery("SELECT * FROM goods");
crs.populate(rs);
%>
```

这三种方法是人们常用的方法。