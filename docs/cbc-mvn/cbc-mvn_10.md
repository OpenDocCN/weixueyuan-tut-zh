# 使用 Maven 开发用户模块的 CRUD（增删改查）

> 原文：[`c.biancheng.net/view/4812.html`](http://c.biancheng.net/view/4812.html)

《使用 Maven 开发 Web 应用详细步骤》教程中介绍了怎样实现一个简单的 Web 应用，讲述了创建 Web 应用、编写代码、在 pom.xml 中配置相关的构件、最后发布测试，接下来再介绍一个经典的实现了 CRUD 的用户 Web 管理应用。

## 创建 Web 工程和初始化数据库

首先，按照教程前面的指导创建一个 Web 工程，创建好工程后，下一步就是初始化数据库了。

这里用的是 MySQL 数据库。建议先安装好数据库，然后创建一个数据库，用如下脚本初始化表。

```

CREATE TABLE mvn_user(
    ur_id int(11) NOT NULL AUTO_INCREMENT,
    ur_user_name varchar(255) DEFAULT NULL,
    ur_password varchar(255) DEFAULT NULL,
    ur_age int(11) DEFAULT NULL,
    ur_status varchar(255) DEFAULT NULL,
    PRIMARY KEY (ur_id)
) ENGINE=InnoDB AUTO_INCREMENT=15 DEFAULT CHARSET=utf8;

INSERT INTO mvn_user(ur_user_name,ur_password,ur_age,ur_status) VALUES ('zhangsan', '123', 11, 'Active');
INSERT INTO mvn_user(ur_user_name,ur_password,ur_age,ur_status) VALUES ('lisi', '123', 13, 'Inactive');
INSERT INTO mvn_user(ur_user_name,ur_password,ur_age,ur_status) VALUES ('wangwu', '123', 13, 'Active');
```

## 添加相关依赖

在整个 Demo 应用中，需要在创建 Web 工程后，额外添加 4 个依赖，分别是 jstl 依赖、MySQL 数据库驱动依赖、JUnit 4.7 依赖和 json-lib 依赖。它们的依赖配置文件如下：

```

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.34</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.7</version>
    <scope>test</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/net.sf.json-lib/json-lib -->
<dependency>
    <groupId>net.sf.json-lib</groupId>
    <artifactId>json-lib</artifactId>
    <version>2.4</version>
    <classifier>jdk15</classifier>
</dependency>
```

## 添加注册代码

MvnUser.java（用户实体类）代码如下所示：

```

public class MvnUser {
    private int urId;
    private String urUserName;
    private String urPassword;
    private int urAge;
    private String urStatus;

    public int getUrId() {
        return urId;
    }

    public void setUrId(int urId) {
        this.urId = urId;
    }

    public String getUrUserName() {
        return urUserName;
    }

    public void setUrUserName(String urUserName) {
        this.urUserName = urUserName;
    }

    public String getUrPassword() {
        return urPassword;
    }

    public void setUrPassword(String urPassword) {
        this.urPassword = urPassword;
    }

    public int getUrAge() {
        return urAge;
    }

    public void setUrAge(int urAge) {
        this.urAge = urAge;
    }

    public String getUrStatus() {
        return urStatus;
    }

    public void setUrStatus(String urStatus) {
        this.urStatus = urStatus;
    }
}
```

DBConnection.java（连接数据库的公共类）代码如下所示：

```

import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.Properties;

/**
* 获取连接的一个工具类，继承 Properties，实现如下封装：
* 1 读取 db.properties 文件
* 2 获取 Connection 连接的方法
* 3 关闭资源的方法
*
* @author Noble
* @version 1.0
*/
public class DBConnection extends Properties {
    private static DBConnection DB_CONN = null;

    /**
     * 构造器方法，被私有化，封装读取 db.properties 逻辑
     *
     */
    private DBConnection() throws Exception {
        InputStream in = DBConnection.class.getClassLoader().getResourceAsStream("db.properties");
        this.load(in);
        // 加载驱动类
        Class.forName(this.getProperty("driverName"));
    }

    /**
     * 单例模式实现，获取 DBConnection 实例的静态方法
     *
     * @return DBConnection DBConnection 实例
     * @throws Exception 初始化 db.properties 出现问题时，会抛异常
     */
    public static DBConnection getInstance() throws Exception {
        if (DB_CONN == null) {
            DB_CONN = new DBConnection();
        }
        return DB_CONN;
    }

    /**
     * 基于驱动和 db.properties 中配置的连接数据库的信息，创建一个新连接返回
     *
     * @return Connection 创建的新连接对象
     *
     */
    public Connection getConnection() {
        Connection conn = null;
        String url = this.getProperty("url");
        String userName = this.getProperty("userName");
        String password = this.getProperty("password");
        //
        try {
            conn = DriverManager.getConnection(url, userName, password);
        } catch (Exception e) {
            throw new RuntimeException("数据库连接错误，请与管理员联系");
        }
        return conn;
    }

    /**
     * 关闭操作数据库后的资源
     *
     * @param conn Connection 对象
     * @param stmt Statement 或 Statement 的子类对象
     * @param rs   ResultSet 对象
     */
    public void close(Connection conn, Statement stmt, ResultSet rs) {
        try {
            if (rs != null)
                rs.close();
            if (stmt != null)
                stmt.close();
            if (conn != null)
                conn.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

MvnUserDAO.java（用户的 DAO 持久层类）代码如下所示：

```

/**import**/
/**
* MvnUser 实体对象的持久层代码，封装了对 MvnUser 实体对象的 CRUD 方法
*
* @author Noble
* @version 1.0
*/
public class MvnUserDAO {
    /**
     * 在数据库中，添加一个新的 MvnUser 对象
     *
     * @param user 需要添加的用户实体对象，该对象需要有用户名、密码、年龄和状态属性
     *
     * @return void
     * @throws RuntimeException 添加失败或出现其它意外
     */
    public void addUser(MvnUser user) {
        DBConnection dbConn = null;
        Connection conn = null;
        PreparedStatement pstmt = null;
        try {
            // 获取 DBConnection 实例
            dbConn = DBConnection.getInstance();
            String sql = "insert into mvn_user (ur_user_name,ur_password,ur_age,ur_status) values(?,?,?,?)";
            // 获取连接对象
            conn = dbConn.getConnection();
            // 基于连接和 sql，获取一个预处理 Statement 对象
            pstmt = conn.prepareStatement(sql);
            // 设置 sql 中占位符的值
            pstmt.setString(1, user.getUrUserName());
            pstmt.setString(2, user.getUrPassword());
            pstmt.setInt(3, user.getUrAge());
            pstmt.setString(4, user.getUrStatus());
            // 执行预处理
            pstmt.executeUpdate();
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            // 关闭资源
            if (dbConn != null)
                dbConn.close(conn, pstmt, null);
        }
    }

    /**
     * 更新 MvnUser 对象。该对象中需要设置年龄、状态和 id 属性，属性和状态是要更新的新值，id 为条件
     *
     * @param user 需要更新的 MvnUser 对象
     *
     * @return void
     * @throws RuntimeException 更新失败或出现其它意外
     */
    public void update(MvnUser user) {
        DBConnection dbConn = null;
        Connection conn = null;
        PreparedStatement pstmt = null;
        try {
            dbConn = DBConnection.getInstance();
            String sql = "update mvn_user set ur_age=?,ur_status=? where ur_id=?";
            conn = dbConn.getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, user.getUrAge());
            pstmt.setString(2, user.getUrStatus());
            pstmt.setInt(3, user.getUrId());

            pstmt.executeUpdate();
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (dbConn != null)
                dbConn.close(conn, pstmt, null);
        }
    }

    /**
     * 删除 MvnUser 对象，该对象中需要有要删除对象的 id 属性，id 属性为删除条件
     *
     * @param user 要删除的 MvnUser 对象
     *
     * @return void
     * @throws RuntimeException 删除失败或出现其它意外
     */
    public void deleteUser(MvnUser user) {
        DBConnection dbConn = null;
        Connection conn = null;
        PreparedStatement pstmt = null;
        try {
            dbConn = DBConnection.getInstance();
            String sql = "delete from mvn_user where ur_id=?";
            conn = dbConn.getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, user.getUrId());

            pstmt.executeUpdate();
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (dbConn != null)
                dbConn.close(conn, pstmt, null);
        }
    }

    /**
     * 根据 id 查询对应的 MvnUser 对象
     *
     * @param id 要查询的 MvnUser 对象的 id
     * @return MvnUser id 对应的 MvnUser 对象，如果没有对象，返回 null
     * @throws RuntimeException 出现意外情况
     */
    public MvnUser findUserById(int id) {
        MvnUser user = null;
        DBConnection dbConn = null;
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            dbConn = DBConnection.getInstance();
            String sql = "select * from mvn_user where ur_id=?";
            conn = dbConn.getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, id);

            rs = pstmt.executeQuery();
            if (rs.next()) {
                user = new MvnUser();
                user.setUrAge(rs.getInt("ur_age"));
                user.setUrId(rs.getInt("ur_id"));
                user.setUrPassword(rs.getString("ur_password"));
                user.setUrStatus(rs.getString("ur_status"));
                user.setUrUserName(rs.getString("ur_user_name"));
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (dbConn != null)
                dbConn.close(conn, pstmt, rs);
        }
        return user;
    }

    /**
     * 根据用户名查询对应的 MvnUser 对象
     *
     * @param userName 要查询的 MvnUser 对象的用户名
     * @return MvnUser 用户对应的 MvnUser 对象，如果没有对象，返回 null
     * @throws RuntimeException 出现意外情况
     */
    public MvnUser findUserByUserName(String userName) {
        MvnUser user = null;
        DBConnection dbConn = null;
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            dbConn = DBConnection.getInstance();
            String sql = "select * from mvn_user where ur_user_name=?";
            conn = dbConn.getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, userName);

            rs = pstmt.executeQuery();
            if (rs.next()) {
                user = new MvnUser();
                user.setUrAge(rs.getInt("ur_age"));
                user.setUrId(rs.getInt("ur_id"));
                user.setUrPassword(rs.getString("ur_password"));
                user.setUrStatus(rs.getString("ur_status"));
                user.setUrUserName(rs.getString("ur_user_name"));
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (dbConn != null)
                dbConn.close(conn, pstmt, rs);
        }
        return user;
    }

    /**
     * 查找数据库中所有的用户对象，以 List 集合的形式返回
     *
     * @return List<MvnUser> 所有用户对象的集合
     * @throws RuntimeException 出现意外情况
     */
    public List<MvnUser> findUsers() {
        List<MvnUser> userList = null;
        DBConnection dbConn = null;
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            dbConn = DBConnection.getInstance();
            String sql = "select * from mvn_user order by ur_id";
            conn = dbConn.getConnection();
            pstmt = conn.prepareStatement(sql);

            rs = pstmt.executeQuery();
            if (rs != null) {
                userList = new ArrayList<MvnUser>();
                MvnUser user = null;
                while (rs.next()) {
                    user = new MvnUser();
                    user.setUrAge(rs.getInt("ur_age"));
                    user.setUrId(rs.getInt("ur_id"));
                    user.setUrPassword(rs.getString("ur_password"));
                    user.setUrStatus(rs.getString("ur_status"));
                    user.setUrUserName(rs.getString("ur_user_name"));

                    userList.add(user);
                }
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (dbConn != null)
                dbConn.close(conn, pstmt, rs);
        }
        return userList;
    }
}
```

UserService.java（用户服务类）代码如下所示：

```

/**
* 对用 CRUD 操作的服务层，封装了 CRUD 在持久化前的必要业务逻辑
*
* @author Noble
* @version 1.0
*/
public class UserService {
    private MvnUserDAO userDAO = new MvnUserDAO();

    /**
     * 创建新的用户
     *
     * @param user 要创建的用户对象
     * @return void
     * @throws RuntimeException 当用户信息不全或用户名已经存在的时候，都会抛出异常
     */
    public void createUser(MvnUser user) {
        // 验证基本的用户信息
        if (user == null || user.getUrUserName() == null || user.getUrPassword() == null) {
            throw new RuntimeException("用户信息不合法");
        }
        // 根据用户名查询用户对象
        MvnUser u = userDAO.findUserByUserName(user.getUrUserName());
        // 如果能查询到用户对象，说明用户已经存在，抛异常
        if (u != null) {
            throw new RuntimeException(user.getUrUserName() + " 用户已存在");
        }
        // 调用 dao 代码，添加一个新用户
        userDAO.addUser(user);
    }

    /**
     * 更新 id 对应用户的年龄和状态信息
     *
     * @param age    要更新用户的新年龄
     * @param status 要更新用户的新状态
     * @param id     要更新用户的 id，这是更新的条件
     *
     * @return void
     */
    public void editUser(int age, String status, int id) {
        MvnUser user = this.userDAO.findUserById(id);
        user.setUrAge(age);
        user.setUrStatus(status);
        this.userDAO.update(user);
    }

    public void deleteUser(int id) {
        MvnUser user = this.userDAO.findUserById(id);
        this.userDAO.deleteUser(user);
    }

    public MvnUser searchUser(int id) {
        MvnUser user = this.userDAO.findUserById(id);
        return user;
    }

    public MvnUser searchUser(String userName) {
        MvnUser user = this.userDAO.findUserByUserName(userName);
        return user;
    }

    public List<MvnUser> searchUsers() {
        List<MvnUser> userList = this.userDAO.findUsers();
        return userList;
    }
}
```

AddUserServlet.java（添加用户 Servlet）代码如下所示：

```

/** import **/

/**
* Servlet implementation class AddUserServlet
*/
public class AddUserServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#service(HttpServletRequest request, HttpServletResponse
     *      response)
     */
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 获取要添加用户的信息
        String userName = request.getParameter("userName");
        String password = request.getParameter("password");
        String ageStr = request.getParameter("age");
        String status = request.getParameter("status");
        int age = 0;
        try {
            // 将年龄字符串，转变成数字（数据库中需要数字类型）
            age = Integer.parseInt(ageStr);
        } catch (Exception e) {
        }
        // 封装成 MvnUser 对象
        MvnUser user = new MvnUser();
        user.setUrAge(age);
        user.setUrPassword(password);
        user.setUrStatus(status);
        user.setUrUserName(userName);

        UserService userService = new UserService();
        String msg = "添加成功";
        try {
            // 调用 service，创建用户
            userService.createUser(user);
        } catch (Exception e) {
            e.printStackTrace();
            msg = "添加失败：" + e.getMessage();
        }
        // 返回添加后的结果提示信息
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.print(msg);
    }

}
```

DeleteUserServlet.java（删除用户 Servlet）代码如下所示：

```

/**import**/
/**
* Servlet implementation class DeleteUserServlet
*/
public class DeleteUserServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#service(HttpServletRequest request, HttpServletResponse
     *      response)
     */
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String idStr = request.getParameter("id");
        int id = 0;
        try {
            id = Integer.parseInt(idStr);
        } catch (Exception e) {
        }
        UserService userService = new UserService();
        String msg = "删除成功";
        try {
            userService.deleteUser(id);
        } catch (Exception e) {
            e.printStackTrace();
            msg = "删除失败:" + e.getMessage();
        }
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.print(msg);
        out.flush();
        out.close();
    }

}
```

EditUserServlet.java（修改用户 Servlet）代码如下所示：

```

/** import **/
/**
* Servlet implementation class EditUserServlet
*/
public class EditUserServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String idStr = request.getParameter("id");
        String status = request.getParameter("status");
        String ageStr = request.getParameter("age");

        int id = 0, age = 0;
        try {
            id = Integer.parseInt(idStr);
        } catch (Exception e) {
        }
        try {
            age = Integer.parseInt(ageStr);
        } catch (Exception e) {
        }
        UserService userService = new UserService();
        String msg = "修改成功";
        try {
            userService.editUser(age, status, id);
        } catch (Exception e) {
            e.printStackTrace();
            msg = "修改失败:" + e.getMessage();
        }
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.print(msg);
        out.flush();
        out.close();
    }

}
```

SearchUserServlet.java（根据用户 Id 或用户名查找用户 Servlet）代码如下所示：

```

/** import **/
/**
* Servlet implementation class SearchUserServlet
*/
public class SearchUserServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 获取查询的方式（根据 id 查询还是根据用户名查询）
        String type = request.getParameter("type");
        UserService userService = new UserService();
        MvnUser user = null;
        // 根据 id 查询
        if ("byId".equals(type)) {
            // 获取 id
            String idStr = request.getParameter("id");
            int id = 0;
            try {
                id = Integer.parseInt(idStr);
            } catch (Exception e) {
            }
            user = userService.searchUser(id);
        } else {
            // 根据用户名查询
            String userName = request.getParameter("userName");
            user = userService.searchUser(userName);
        }
        // 设置返回的响应为 json 响应
        response.setContentType("text/json;charset=UTF-8");
        PrintWriter out = response.getWriter();
        // 将查询的用户对象，转变成 json 格式的字符串，写入响应返回
        out.print(JSONObject.fromObject(user));
        out.flush();
        out.close();
    }

}
```

SearchUsersServlet.java（查询所有用户 Servlet）代码如下所示：

```

/** import **/
/**
* Servlet implementation class SearchUsersServlet
*/
public class SearchUsersServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#service(HttpServletRequest request, HttpServletResponse
     *      response)
     */
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        UserService userService = new UserService();
        List<MvnUser> userList = userService.searchUsers();
        // 将查询出的用户集合，写于 request 属性中
        request.setAttribute("userList", userList);
        // 转向 userList.jsp 页面
        request.getRequestDispatcher("/userList.jsp").forward(request, response);
    }

}
```

userList.jsp（显示用户列表 jsp）代码如下所示：

```

<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<table width="100%" border="1">
    <tr>
        <td width="51" bgcolor="#CCCCCC">
            <div align="center">
                <strong><span class="STYLE2">选择</span></strong>
            </div>
        </td>
        <td width="111" bgcolor="#CCCCCC">
            <div align="center">
                <strong>序号</strong>
            </div>
        </td>
        <td width="137" bgcolor="#CCCCCC">
            <div align="center">
                <strong>用户名</strong>
            </div>
        </td>
        <td width="105" bgcolor="#CCCCCC">
            <div align="center">
                <strong>年龄</strong>
            </div>
        </td>
        <td width="101" bgcolor="#CCCCCC">
            <div align="center">
                <strong>状态</strong>
            </div>
        </td>
    </tr>

    <c:forEach items="${userList }" var="_user" varStatus="status">
        <tr>
            <c:choose>
                <c:when test="${status.index%2==0 }">
                    <tr bgcolor="#FFFFFF">
                </c:when>
                <c:otherwise>
                    <tr bgcolor="#99FFFF">
                </c:otherwise>
            </c:choose>
            <td><input type="checkbox" name="checkbox" value="${_user.urId }"></td>
            <td>${status.index+1 }</td>
            <td>${_user.urUserName }</td>
            <td>${_user.urAge }</td>
            <td>${_user.urStatus }</td>
        </tr>
    </c:forEach>
</table>
```

index.jsp，进入首页（框架 jsp），代码如下所示：

```

<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>测试用户 CRUD 操作</title>
<script type="text/javascript" src="js/jquery-1.8.2.min.js"></script>
<script type="text/javascript">
    $(document).ready(function() {
        // 页面加载完后，自动发 searchUsersServlet 请求，加载到 userListDiv 中显示
        $("#userListDiv").load("searchUsersServlet");
    });
    // 新增 按钮事件触发函数
    function toAdd() {
        // 获取 addForm 中的请求信息
        var _data = $("#addForm").serialize();
        //alert(_data);
        // 发添加新用户的 Ajax 请求
        $.ajax({
            type : 'post',
            url : 'addUserServlet',
            data : _data,
            success : function(msg) {
                alert(msg);
                // 更新最新的用户列表信息
                $("#userListDiv").load("searchUsersServlet");
            }
        });
    }
    function toEdit() {
        var _data = $("#editForm").serialize();
        alert(_data);
        $.ajax({
            type : 'post',
            url : 'editUserServlet',
            data : _data,
            success : function(msg) {
                alert(msg);
                $("#userListDiv").load("searchUsersServlet");
            }
        });
    }

    function toDelete() {
        var chks = $("input[name='checkbox']:checked");
        if (chks.length == 0) {
            alert("请选择要删除的用户");
        } else if (chks.length > 1) {
            alert("一次只能删除一个用户");
        } else {
            var to = confirm("您确定要删除选中的用户?");
            if (to) {
                var _data = "id=" + chks.val();
                $.ajax({
                    type : 'post',
                    data : _data,
                    url : 'deleteUserServlet',
                    success : function(msg) {
                        alert(msg);
                        $("#userListDiv").load("searchUsersServlet");
                    }
                });
            }
        }
    }

    function toShowAdd() {
        $("#LayerAdd").show(1000);
    }
    function toShowEdit() {
        //alert($("input[name='checkbox']:checked").length);
        var chks = $("input[name='checkbox']:checked");
        if (chks.length == 0) {
            alert("请选择要编辑的用户");
        } else if (chks.length > 1) {
            alert("一次只能修改一个用户");
        } else {
            var _data = "id=" + chks.val();
            $.ajax({
                type : 'post',
                data : _data,
                url : 'searchUserServlet?type=byId',
                dataType : 'json',
                success : function(msg) {
                    $("#editForm #id").val(msg.urId);
                    $("#editForm #userName").val(msg.urUserName);
                    $("#editForm #age").val(msg.urAge);
                    $("#editForm #status").val(msg.urStatus);
                    //alert($("#editForm #age").val());
                    $("#LayerEdit").show(1000);
                }
            });
        }

    }
    function toCloseAdd() {
        $("#LayerAdd").hide(1000);
    }
    function toCloseEdit() {
        $("#LayerEdit").hide(1000);
    }
</script>
<style type="text/css">
<!--
.STYLE2 {
    color: #000000
}

#LayerAdd {
    position: absolute;
    left: 113px;
    top: 183px;
    width: 434px;
    height: 193px;
    z-index: 1;
    background-color: #99FFFF;
    display: none;
}

#LayerEdit {
    position: absolute;
    left: 113px;
    top: 183px;
    width: 434px;
    height: 193px;
    z-index: 1;
    background-color: #99FFFF;
    display: none;
}
-->
</style>
</head>

<body>
    <div id="LayerAdd">
        <form name="addForm" name="addForm" id="addForm" method="post"
            action="">

            <table width="98%" border="0" align="center" cellpadding="0"
                cellspacing="0">
                <tr>
                    <td colspan="2" align="center"><strong><BR>添加新用户<br></strong></td>
                </tr>
                <tr>
                    <td width="47%" align="right">用户名：</td>
                    <td width="53%"><input name="userName" type="text"
                        id="userName"></td>
                </tr>
                <tr>
                    <td align="right">密码：</td>
                    <td><input name="password" type="password" id="password"></td>
                </tr>
                <tr>
                    <td align="right">年龄：</td>
                    <td><input name="age" type="text" id="age"></td>
                </tr>
                <tr>
                    <td colspan="2">&nbsp;</td>
                </tr>
                <tr>
                    <td colspan="2" align="center">
                     <input type="button" name="Submit4" value="添加" onclick="toAdd()">
                     <input type="button" name="Submit5" value="关闭" onclick="toCloseAdd()">
                    </td>
                </tr>
            </table>
        </form>
    </div>

    <div id="LayerEdit">
        <form name="editForm" id="editForm" method="post" action="">
            <input type="hidden" name="id" id="id" />
            <table width="98%" border="0" align="center" cellpadding="0"
                cellspacing="0">
                <tr>
                    <td colspan="2" align="center">
                     <strong><br>修改用户信息<br> </strong>
                    </td>
                </tr>
                <tr>
                    <td width="47%" align="right">用户名：</td>
                    <td width="53%">
                     <input name="userName" type="text" id="userName" readonly="readonly">
                    </td>
                </tr>
                <tr>
                    <td align="right">年龄：</td>
                    <td><input name="age" type="text" id="age"></td>
                </tr>
                <tr>
                    <td align="right">状态：</td>
                    <td>
                     <select name="status" id="status">
                            <option value="Active">Active</option>
                            <option value="Inactive">Inactive</option>
                            <option value="Locked">Locked</option>
                            <option value="Deleted">Deleted</option>
                     </select>
                    </td>
                </tr>
                <tr>
                    <td colspan="2">&nbsp;</td>
                </tr>
                <tr>
                    <td colspan="2" align="center">
                     <input type="button" name="Submit4" value="修改" onclick="toEdit()">
                     <input type="button" name="Submit5" value="关闭" onclick="toCloseEdit()">
                    </td>
                </tr>
            </table>
        </form>
    </div>

    <p>&nbsp;</p>
    <p>测试用户 CRUD 页面</p>
    <table width="539" border="1">
        <tr>
            <td colspan="5" align="right">
             <input type="button" name="Submit" value="新增" onclick="toShowAdd()">
             <input type="submit" name="Submit2" value="修改" onclick="toShowEdit()">
             <input type="button" name="Submit3" value="删除" onclick="toDelete()">
            </td>
        </tr>

        <tr>
            <td>
                <div id="userListDiv"></div>
            </td>
        </tr>
    </table>
    <p>&nbsp;</p>
</body>
</html>
```

db.properties，数据库信息配置文件，代码如下所示：

userName=root
password=root
url=jdbc:mysql://localhost:3306/mvn_db
driverName=com.mysql.jdbc.Driver

## 构建项目

代码写好了，接下来是在 pom.xml 中添加发布 Web 应用和同 Web 服务器相关的插件，这些在前面的简易 Web 案例中已提到，这里就直接贴出当前 Web 应用到的插件配置，代码如下：

```

<build>
    <plugins>
        <plugin>
            <groupId>org.mortbay.jetty</groupId>
            <artifactId>maven-jetty-plugin</artifactId>
            <version>6.1.26</version>
            <configuration>
                <webAppSourceDirectory>${basedir}/src/main/webapp</webAppSourceDirectory>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>2.0.2</version>
            <configuration>
                <source>1.5</source>
                <target>1.5</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 测试

右击“工程”，选择 Run As→Maven build… 命令，在 Goals 后面输入“jetty:run”目标，运行 jetty 服务器。在浏览器中输入“http://localhost:8080/MvnDemo03/index.jsp”进行运行测试。