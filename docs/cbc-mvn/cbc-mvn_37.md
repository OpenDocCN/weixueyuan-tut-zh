# Maven 资源过滤的配置

> 原文：[`c.biancheng.net/view/5285.html`](http://c.biancheng.net/view/5285.html)

为了适应环境的变化，需要使用 Maven 属性将这些将会变化的部分提取出来，用一个特殊的方式描述它们。这个道理同在代码中定义变量，用一个变量描述一个值一样。

比如，上面提到的数据库配置，就可以在配置文件中用 Maven 属性代替以前写的配置信息，例如：

db.jdbc.driverClass=${db.driver}
db.jdbc.connectionURL=${db.url}
db.jdbc.username=${db.username}
db.jdbc.password=${db.password}

这样就定义四个 Maven 属性，它们的名称分别是 db.driver、db.url、db.username、db.password。用户可以根据自己的需要和习惯自定义这些名称。

接下来，就需要在某个地方给这些 Maven 属性赋予真实的，能用来连接数据库的值了。

可以按教程前面定义 Maven 属性的方式，在 pom.xml 的 properties 中定义 Maven 属性，并且给它们赋值。但是这样不方便根据环境的变化而自动切换，所以这里介绍一下用 profile 的方式进行定义。例如：

```

<profiles>
    <profile>
        <id>dev_evn</id>
        <properties>
            <db.driver>com.mysql.jdbc.Driver</db.driver>
            <db.url>jdbc:mysql://localhost:3306/test</db.url>
            <db.username>root</db.username>
            <db.password>root</db.password>
        </properties>
    </profile>
</profiles>
```

这里的声明方式同在 properties 元素中声明的方式实际上是一样的。唯一不同的是，把它们放在 profile 中包装，同时用一个名称为“dev_evn”的 id 标识，这样就可以将开发环境的配置同其他环境的配置区分开。

现在定义了 Maven 属性，同时在配置文件中也使用这些定义好的 Maven 属性，是不是就可以正常构建起来了呢？当然还是不行的，因为 Maven 属性只能在 pom.xml 中才能被解析出来。

也就是说，＄{db.username} 在 pom 中可以被 Maven 解析成 root。但是如果放在资源目录（src/main/resources）下的文件中，Maven 在构建的时候就不能将＄{db.username} 转变成 root。这时候就需要想办法让 Maven 能解析资源文件中的 Maven 属性。

在 Maven 中，对资源文件进行处理的工作是由 maven resource plugin 插件完成的，它默认的工作是将项目主资源文件复制到主代码编译输出目录中，将测试资源文件复制到测试代码编译输出目录中。

当然，也可以通过一些简单的 pom 配置，让 maven resource plugin 插件能解析资源文件中的 Maven 属性，也就是开启资源过滤功能。

Maven 是在超级 pom 中定义主资源目录和测试资源目录。为了开启资源过滤功能，需要在 pom 中添加一个 filtering 配置，代码如下：

```

<resources>
    <resource>
        <directory>${project.basedir}/src/main/resources</directory>
        <filtering>true</filtering>
    </resource>
</resources>
<testResources>
    <testResource>
        <directory>${project.basedir}/src/test/resources</directory>
        <filtering>true</filtering>
    </testResource>
</testResources>
```

根据前面的介绍，Maven 可以实现多个主资源目录和测试资源目录的配置，而且可以根据需要将部分资源目录的过滤功能开启，而屏蔽其他的资源目录的过滤功能。

按照项目的需要，遵循前面的规则，在 pom 中配置好后就可以在 mvn 命令中用`-P`参数激活指定 id 对应的 profile 进行动态构建了。比如执行：

Mvn clean install -Pdev_evn

基于配置好的 Maven，在执行时会将配置的数据库连接信息构建到 Maven 项目中。