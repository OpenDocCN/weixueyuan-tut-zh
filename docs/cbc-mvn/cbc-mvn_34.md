# Maven 测试报告的生成和查看

> 原文：[`c.biancheng.net/view/5282.html`](http://c.biancheng.net/view/5282.html)

在 Maven 构建过程中，除了可以通过查看命令行的提示信息了解测试状况和结果外，还可以使用 Maven 的相关插件生成专业统一的测试报告，这样方便归档、查看和提交测试状况与结果。

## 基本测试报告

在默认情况下，maven-surefire-plugin 插件会在 Maven 项目的 target/surefire-reports 目录下生成两种格式的错误报告：一种是文本格式；另一种是与 JUnit 兼容的 XML 格式。

下面是 MvnSSMDemo.Service.Impl 项目中 TestUserServiceImpl 测试案例运行后的报告样例。

#### 1\. 文本格式测试报告

-------------------------------------------------------------------------------
Test set: cn.com.mvn.ssh.demo.service.Impl.TestUserServiceImpl
-------------------------------------------------------------------------------
Tests run: 6, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 4.906 sec

#### 2\. XML 格式测试报告

```

<?xml version="1.0" encoding="UTF-8" ?>
<testsuite failures="0" time="4.773" errors="0" skipped="0" tests="6"
    name="cn.com.mvn.ssh.demo.service.Impl.TestUserServiceImpl">
    <properties>
        <property name="java.runtime.name"
            value="Java(TM) SE Runtime Environment" />
        ...
    </properties>
    <testcase time="2.669"
        classname="cn.com.mvn.ssh.demo.service.Impl.TestUserServiceImpl"
        name="testEditUser" />
        ...
</testsuite>
```

如果要生成 HTML 的测试报告，需要在 pom.xml 中添加 maven-surefire-report-plugin 插件的配置，样例配置代码如下：

```

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-report-plugin</artifactId>
            <version>2.12.2</version>
            <configuration>
                <showSuccess>false</showSuccess>
            </configuration>
        </plugin>
    </plugins>
</build>
```

运行测试后，Maven 会自动生成 HTML 版的测试报告，内容同文本和 XML 版本的一样，只是展现的形式是 HTML，方便项目相关人员和客户查看。

## 测试覆盖率报告

测试报告主要报告的是测试代码的运行结果是否正确，至于对软件的测试质量，要依靠测试代码本身的设计和实现。设计和实现得比较详细周到的话，测试的质量就高，否则就会有很多情况没有被测试到，造成软件的漏洞不能被及时发现。

所以为了提高软件本身的质量，除了要设计编写测试案例代码进行测试之外，还必须对代码测试的范围进行一个控制，从而进一步保证软件代码的质量。所以测试覆盖率也是衡量软件代码质量的一个重要参考指标。

Cobertura 是一个优秀的测试覆盖率统计工具。在 Maven 项目中，用户可以通过集成 cobertura-maven-plugin 插件，再执行 Maven 命令“mvn cobertura:cobertura”，就可以生成测试覆盖率报告。

当然，要能顺利地生成测试覆盖率报告，需要在 pom.xml 中配置 Cobertura 插件，样例配置代码如下：

```

<build>
    <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>cobertura-maven-plugin</artifactId>
            <version>2.5.1</version>
        </plugin>
    </plugins>
</build>
```

Cobertura 除了有前面介绍的生成测试报告的命令外，还有如下命令。

*   mvn cobertura:help，查看 Cobertura 插件的帮助。
*   mvn cobertura:clean，清空 Cobertura 插件的运行结果。
*   mvn cobertura:check，运行 Cobertura 插件的检查任务。
*   mvn cobertura:cobertura，运行 Cobertura 插件的检查任务并生成报表，报表生成在 target/site/cobertura 目录下。
*   cobertura:dump-datafile,Cobertura Datafile Dump Mojo。
*   mvn cobertura:instrument,Instrument the compiled classes。

在 target 文件夹下出现了一个 site 目录，下面是一个静态站点，里面就是单元测试的覆盖率报告。