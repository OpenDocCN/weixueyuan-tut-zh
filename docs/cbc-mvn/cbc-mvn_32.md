# JUnit 和 TestNG：Java 单元测试框架

> 原文：[`c.biancheng.net/view/5278.html`](http://c.biancheng.net/view/5278.html)

目前，常用的 Java 单元测试框架是 JUnit 和在 JUnit 基础上进一步扩展的 TestNG。为了能很好地在 Maven 中完成测试案例的执行和形成测试报告，这里介绍一下怎样在 JUnit 和 TestNG 框架下编写测试代码。

## JUnit 单元测试框架

JUnit 是由 Erich Gamma 和 Kent Beck 编写的一个回归测试框架，是一个开放源代码的 Java 测试框架，可以在它的基础上编写和运行可重复的测试。

JUnit 单元测试框架有如下几个特点。

*   使用断言测试结果。
*   能共享测试数据。
*   方便注册和运行测试。
*   支持图形化测试。

JUnit 单元测试框架的安装比较简单，只需下载 JUnit 的最新压缩包在本地解压后，配置好 JUNIT_HOME 环境变量，并且在 CLASSPATH 目录中追加好 JUnit 的 jar 包就可以了。

对于 IDE 环境的用户，只需将 JUnit 的 jar 包添加到项目的 build path 中就可以了。接下来回顾梳理（以前样例中有编写，只是没有系统介绍）一下怎样在一个 Maven 项目中基于 JUnit 编写测试案例。

在 Maven 项目中，基于 JUnit 编写测试案例一般要两步：一是在 pom.xml 中添加 JUnit 依赖；二是基于 JUnit 规范编写测试代码。

如下所示是 MvnSSMDemo.Service.Impl 项目中关于 JUnit 的配置。

在 pom.xml 中的 JUnit 依赖配置。

<dependencies>
    <dependency>
        <groupId>cn.com.mvnssh.demo</groupId>
        <artifactId>MvnSSHDemo.DAO</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>cn.com.mvn.ssh.demo</groupId>
        <artifactId>MvnSSHDemo.Service</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>cn.com.mvn.ssm.demo</groupId>
        <artifactId>MvnSSMDemo.DAO.MyBatis</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <scope>test</scope>
    </dependency>
</dependencies>

TestUserServiceImpl.java 类代码如下所示：

```

package cn.com.mvn.ssh.demo.service.impl;

import java.util.List;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import cn.com.mvn.ssh.demo.entity.MvnUser;
import cn.com.mvn.ssh.demo.entity.Status;
import cn.com.mvn.ssh.demo.service.IUserService;
import junit.framework.Assert;

public class TestUserServiceImpl {
    private IUserService userService;
    private ApplicationContext ctx = null;

    @Before
    public void init() {
        this.ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        this.userService = (IUserService) ctx.getBean("userService");
    }

    @Test
    public void testCreateUser() {
        MvnUser user = new MvnUser();
        user.setUrAge(11);
        user.setUrPassword("11");
        user.setUrStatus(Status.ACTIVE.getStatus());
        user.setUrUserName("service1");
        this.userService.createUser(user);
        MvnUser u = this.userService.searchUser("service1");
        boolean bool = u != null && u.getUrAge() == 11 && u.getUrStatus().equals(Status.ACTIVE.getStatus());
        Assert.assertTrue(bool);
        // 删除用户
        this.userService.deleteUser(u.getUrId());
    }

    @Test
    public void testEditUser() {
        MvnUser user = new MvnUser();
        user.setUrAge(11);
        user.setUrPassword("11");
        user.setUrStatus(Status.ACTIVE.getStatus());
        user.setUrUserName("service1");
        this.userService.createUser(user);
        MvnUser u = this.userService.searchUser("service1");
        this.userService.editUser(88, Status.INACTIVE.getStatus(), u.getUrId());
        u = this.userService.searchUser("service1");
        Assert.assertTrue(u.getUrAge() == 88 && u.getUrStatus().equals(Status.INACTIVE.getStatus()));
        this.userService.deleteUser(u.getUrId());
    }

    @Test
    public void testDeleteUser() {
        MvnUser user = new MvnUser();
        user.setUrAge(11);
        user.setUrPassword("11");
        user.setUrStatus(Status.ACTIVE.getStatus());
        user.setUrUserName("service1");
        this.userService.createUser(user);
        MvnUser u = this.userService.searchUser("service1");
        this.userService.deleteUser(u.getUrId());
        MvnUser u2 = this.userService.searchUser(u.getUrId());
        Assert.assertTrue(u != null && u2 == null);
    }

    @Test
    public void testSearchUserById() {
        MvnUser user = this.userService.searchUser(1);
        Assert.assertNotNull(user);
    }

    @Test
    public void testSearchUserByUserName() {
        MvnUser user = this.userService.searchUser("zhangsan");
        Assert.assertNotNull(user);
    }

    @Test
    public void testSearchUsers() {
        List<MvnUser> userList = this.userService.searchUsers();
        Assert.assertTrue(userList != null && userList.size() > 0);
    }

    @After
    public void destory() {
        this.userService = null;
        this.ctx = null;
    }
}
```

pom.xml 中的 JUnit 依赖配置在这里就不过多重复了，这里主要说明测试代码的注意事项。

1）在 Maven 项目中，测试代码有专门的默认目录：src/test/java。

2）一般测试案例代码的包与要测试的目标类的包一样。

3）测试代码的类的命名一般是“Test+目标测试类的类名”。

4）测试代码中的方法有三种。

*   使用 @Before 标记的，实现初始化执行测试代码需要的资源。
*   使用 @Test 标记的，跟测试目标类的每个方法一一对应的测试代码。
*   使用 @After 标记的，完成测试后需要释放的资源。

5）测试方法的逻辑。

*   准备好测试数据。
*   根据测试工具和用户需求（目标代码的实现），确定期望结果。
*   执行测试方法获取实际结果。
*   断言实际结果是否同期望结果一致。

## TestNG 测试框架

TestNG 是一个测试框架，也是一个开源的自动化测试框架。很多人把 TestNG 理解成 JUnit、特别是 JUnit4 的下一代。实际上它不只是简单扩展 JUnit，它是一个灵感源于 JUnit，目的是为了更优于 JUnit 的自动测试框架，跟 JUnit 是独立的。

TestNG 消除了大部分旧框架的限制，使开发人员能够编写更加灵活、更加强大的测试程序，而且很大程度上借鉴了 Java 注解，可以使测试代码更好地同 Java 新特征整合。

相对其他测试框架，TestNG 有如下自身的特点。

*   使用简单的注解说明测试方法。
*   TestNG 使用 Java 和面向对象编程。
*   支持综合测试。
*   独立的编译时间、独立的运行测试代码的配置和数据。
*   灵活的运行时配置。
*   支持测试组设置和运行。
*   支持依赖测试、并行测试、负载测试和局部测试。
*   灵活的插件 API。
*   支持多线程测试。

在 Maven 项目中编写和运行 TestNG 是比较方便的。首先要移除以前在 pom 中配置的 JUnit 依赖，添加 TestNG 依赖，代码如下所示。

<dependency>
    <groupId>org.testng</groupId>
    <artifactId>testng</artifactId>
    <version>5.9</version>
    <scope>test</scope>
    <classifier>jdk15</classifier>
</dependency>

同 JUnit 类似，TestNG 的依赖范围是 test。另外，TestNG 使用 classifier jdk15 和 jdk14 为不同的 Java 平台提供支持。

接下来在测试代码中将以前引用的 JUnit 的注解、类改成 TestNG 的。注解名称和类名都一样，只是包名不同，常用的类如下。

*   org.testng.annotations.Test，测试方法的注解。
*   org.testng.annotations.BeforeMethod，测试方法运行前执行的方法注解。
*   org.testng.annotations.AfterMethod，测试方法运行后执行的方法注解。
*   org.testng.annotations.BeforeClass，所有测试方法运行前执行的方法注解。
*   org.testng.annotations.AfterClass，所有测试方法运行后执行的方法注解。
*   org.testng.Assert，断言类。

同运行 JUnit 一样，直接使用 mvn test 命令，Maven 会自动执行符合命名模式的测试类。

TestNG 除了可以同 JUnit 一样自动执行符合命名模式的测试类外，还可以通过 testng.xml 配置文件需要运行的测试集合。例如，可以在 Maven 项目的根目录下创建一个 testng.xml 文件，代码如下：

```

<?xml version="1.0" encoding="utf-8" ?>
<suite name="TestSuite" verbose="1">
    <test name=”test1”>
        <classes>
            <class name="cn.com.mvn.demo.TestNGDemo"/>
        </classes>
    </test>
</suite>
```

同时，在 maven-surefire-plugin 中声明 testng.xml，代码如下：

```

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.16</version>
    <configuration>
        <suiteXmlFiles>
            <suiteXmlFile>testng.xml</suiteXmlFile>
        </suiteXmlFiles>
    </configuration>
</plugin>
```

另外，TestNG 相对 JUnit 还有一个优势，就是可以使用注解的方式对测试方法进行分组标记。在运行的时候可以指定只执行哪个组的测试方法，或哪些组的测试方法，如下所示。

@Test{groups = {"group1","group2"}}

表示将对应的方法加入 group1 组和 group2 组。

接下来，可以在 maven-surefire-plugin 插件中配置运行哪些组，代码如下：

```

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.16</version>
    <configuration>
        <groups>group1,group3</groups>
    </configuration>
</plugin>
```

表示执行当前 TestNG 的时候，只会执行 group1 和 group2 两个组的测试方法。