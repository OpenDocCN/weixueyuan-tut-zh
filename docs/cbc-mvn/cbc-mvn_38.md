# Maven profile 配置管理及激活 profile 的几种方式

> 原文：[`c.biancheng.net/view/5286.html`](http://c.biancheng.net/view/5286.html)

为了实现不同环境构建的不同需求，这里使用到了 profile。因为 profile 能够在构建时修改 pom 的一个子集，或者添加额外的配置元素。接下来介绍 Maven 中对 profile 的配置和激活。

## 针对不同环境的 profile 的配置

为了体现不同环境的不同构建，需要配置好不同环境的 profile，代码如下：

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
    <profile>
        <id>test_evn</id>
        <properties>
            <db.driver>com.mysql.jdbc.Driver</db.driver>
            <db.url>jdbc:mysql://localhost:3306/test_db</db.url>
            <db.username>root</db.username>
            <db.password>root</db.password>
        </properties>
    </profile>
</profiles>
```

在两个不同的 profile 中，配置了同样的属性，不一样的值。按照前面的介绍，在开发时可以用 mvn 命令后面添加“-Pdev_evn”激活“dev_evn profile”。

## 激活 profile 配置

在 Maven 中，可以选用如下的方式激活 profile。

#### 1\. 命令行激活

用户可以在 mvn 命令行中添加参数“-P”，指定要激活的 profile 的 id。如果一次要激活多个 profile，可以用逗号分开一起激活。例如：

mvn clean install -Pdev_env,test_evn

这个命令就同时激活了 id 为“dev_evn”和“test_evn”的两个 profile。

#### 2\. Settings 文件显示激活

如果希望某个 profile 默认一直处于激活状态，可以在 settings.xml 中配置 activeProfiles 元素，指定某个 profile 为默认激活状态，样例配置代码如下：

<settings>
    ...
    <activeProfiles>
        <activeProfile>dev_evn</activeProfile>
    </activeProfiles>
    ...
</settings>

#### 3\. 系统属性激活

可以配置当某个系统属性存在时激活 profile，代码如下：

<profiles>
    <profile>
        ...
        <activation>
            <property>
                <name>profileProperty</name>
            </property>
        </activation>
    </profile>
</profiles>

甚至还可以进一步配置某个属性的值是什么时候激活，例如：

<profiles>
    <profile>
        ...
        <activation>
            <property>
                <name>profileProperty</name>
                <value>dev</value>
            </property>
        </activation>
    </profile>
</profiles>

这样就可以在 mvn 中用“-D”参数来指定激活，例如：

Mvn clean install -DprofileProperty=dev

表示激活属性名称为 profileProperty，值为 dev 的 profile。

实际上这也是一种命令激活 profile 的方法，只是用的是“-D”参数指定激活的属性和值，而前面的是用的“-P”参数指定激活的 profile 的 id 而已。

#### 4\. 操作系统环境激活

用户可以通过配置指定不同操作系统的信息，实现不同操作系统做不同的构建。例如：

<profiles>
    <profile>
        <activation>
            <os>
                <name>Window XP</name>
                <family>Windows</family>
                <arch>x86</arch>
                <version>5.1.2600</version>
            </os>
        </activation>
    </profile>
</profiles>

family 的值是 Windows、UNIX 或 Mac。name 为操作系统名称。arch 为操作系统的架构。version 为操作系统的版本。具体的值可以通过查看环境中的系统属性“os.name”“os.arch”和“os.version”获取。

#### 5\. 文件存在与否激活

当然，也可以通过配置判断某个文件存在与否来决定是否激活 profile，样例配置代码如下：

<profiles>
    <profile>
        <activation>
            <file>
                <missing>t1.properties</missing>
                <exists>t2.properties</exists>
            </file>
        </activation>
    </profile>
</profiles>

#### 6\. 默认激活

最后，还可以配置一个默认的激活 profile，例如：

<profiles>
    <profile>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
</profiles>

需要注意的是，如果 pom 中有任何一个 profile 通过其他方式被激活的话，所有配置成默认激活的 profile 都会自动失效。 可以使用如下命令查看当前激活的 profile。

Mvn help:active-profiles

也可以使用如下命令查看所有的 profile。

Mvn help:all-profiles

## profile 的种类

前面介绍了 profile 的意义和激活方式。那么在 Maven 中，有哪些 profile？如何配置呢？

根据 profile 配置的位置不同，可以将 profile 分成如下几种。

#### 1）pom.xml

pom.xml 中声明的 profile 只对当前项目有效。

#### 2）用户 settings.xml

在用户目录下的“.m2/settings.xml”中的 profile，对本机上的该用户的所有 Maven 项目有效。

#### 3）全局 settings.xml

在 Maven 安装目录下 conf/settings.xml 中配置的 profile，对本机上所有项目都有效。

为了不影响其他用户且方便升级 Maven，一般配置自己的 settings.xml，不要轻易修改全局的 settings.xml。同样的道理，一般不需要修改全局 settings.xml 中的 profile。

不同类型的 profile 中可以声明的 pom 元素是不一样的，pom.xml 中的 profile 能够随同 pom.xml 一起提交到代码仓库中，被 Maven 安装到本地仓库里面，并且能被部署到远程 Maven 仓库中。也就是说，可以保证 profile 伴随特定的 pom.xml 一起存在。所以它可以修改或者添加很多 pom 元素，例如：

```

<project>
    <repositories></repositories>
    <pluginRepositories></pluginRepositories>
    <dependencies></dependencies>
    <dependencyManagement></dependencyManagement>
    <modules></modules>
    <properties></properties>
    <reporting></reporting>
    <build>
        <plugins></plugins>
        <defaultGoal></defaultGoal>
        <resources></resources>
        <testResources></testResources>
        <finalName></finalName>
    </build>
</project>
```

如上代码所示，在 pom 中的 profile 元素比较多，可以添加或修改插件配置、项目资源目录、测试资源目录配置和项目构建的默认名称等。

除了 pom 中的 profile 外，其他外部的 profile 可以配置的元素相对就少些，因为那些外部 profile 无法保证同项目中的 pom.xml 一起发布。

如果在外部 profile 中配置了项目依赖，开发用户可以在本地编译，但是因为依赖配置没有随同 pom.xml 一起发布部署到仓库中，别的用户下载了该项目后，就会因为缺少依赖而失败。

为了避免这样的不一致情况，很多在 pom 的 profile 可以出现的元素不允许在外部 profile 中出现。

在外部 profile 可以声明的元素如下：

```

<project>
    <repositories></repositories>
    <pluginRepositories></pluginRepositories>
    <properties></properties>
</project>
```

这些外部 profile 元素不足以影响项目的正常构建，只会影响项目的仓库和 Maven 属性。