# Maven 插件的调用和解析

> 原文：[`c.biancheng.net/view/4980.html`](http://c.biancheng.net/view/4980.html)

本节我们简介 Maven 插件的调用和解析。

## 调用插件

一般情况下，用户在构建工程时是通过 Maven 调用执行配置好的插件。当然，这里也可以用命令行执行。比如前面查看插件信息的命令，就是在调用 help 插件的 describe 目标来完成查看任务的。同样，运行如下命令。

Mvn dependency:tree

就是在命令行中执行 maven-dependency-plugin 的 tree 目标，列出当前项目的依赖树，结果内容如下：

[INFO] cn.com.mvn.ssm.demo:MvnSSMDemo.Service.Impl:jar:0.0.1-SNAPSHOT
[INFO] +- cn.com.mvn.ssh.demo:MvnSSHDemo.DAO:jar:0.0.1-SNAPSHOT:compile
[INFO] +- cn.com.mvn.ssh.demo:MvnSSHDemo.Service:jar:0.0.1-SNAPSHOT:compile
[INFO] +- cn.com.mvn.ssm.demo:MvnSSMDemo.DAO.MyBatis:jar:0.0.1-SNAPSHOT:test
[INFO] |  +- cn.com.mvn.pom:SpringPOM:pom:0.0.1-SNAPSHOT:test
[INFO] |  +- org.mybatis:mybatis:jar:3.4.0:test
[INFO] |  +- org.mybatis:mybatis-spring:jar:1.3.0:test
[INFO] |  +- org.mybatis.generator:mybatis-generator-core:jar:1.3.2:test
[INFO] |  +- commons-dbcp:commons-dbcp:jar:1.4:test
[INFO] |  |  \- commons-pool:commons-pool:jar:1.5.4:test
[INFO] |  \- mysql:mysql-connector-java:jar:5.1.34:test
[INFO] +- junit:junit:jar:4.7:test
[INFO] +- org.springframework:spring-core:jar:4.2.7.RELEASE:compile
[INFO] |  \- commons-logging:commons-logging:jar:1.2:compile
[INFO] +- org.springframework:spring-aop:jar:4.2.7.RELEASE:compile
[INFO] |  \- aopalliance:aopalliance:jar:1.0:compile
[INFO] +- org.springframework:spring-beans:jar:4.2.7.RELEASE:compile
[INFO] +- org.springframework:spring-context:jar:4.2.7.RELEASE:compile
[INFO] |  \- org.springframework:spring-expression:jar:4.2.7.RELEASE:compile
[INFO] +- org.springframework:spring-context-support:jar:4.2.7.RELEASE:compile
[INFO] +- org.springframework:spring-web:jar:4.2.7.RELEASE:compile
[INFO] +- org.springframework:spring-webmvc:jar:4.2.7.RELEASE:compile
[INFO] +- org.springframework:spring-aspects:jar:4.2.7.RELEASE:compile
[INFO] |  \- org.aspectj:aspectjweaver:jar:1.8.9:compile

总结起来，使用命令行执行 Maven 插件的语法如下：

maven <插件名称|前缀>:<目标> [-D 参数名=参数值 ...]

## 解析插件

在输入 mvn dependency:tree 命令查看当前工程的依赖树时，用到的是 dependency 插件。这里有没有感觉到疑惑，使用插件的话一般要指定插件的坐标信息（groupId、artifactId、version）才能唯一指定一个插件，为什么这里只是输入了 dependency 就可以指定使用的是 maven-dependency-plugin 插件呢？

其实这是 Maven 为了方便用户提供的一种简单方式，可以使用插件的前缀来指定插件。接下来详细介绍一下 Maven 的运行机制，来从本质上把握 mvn 命令的语法。

#### 1\. 插件仓库

同依赖构件一样，插件构件也是基于坐标存储在 Maven 仓库中的。在需要时，Maven 先从本地仓库中查找插件，如果没有，就从远程仓库查找。找到插件后，下载到本地仓库使用。

需要注意的是，Maven 会区别对待依赖的远程仓库和插件的远程仓库。以前在 setting 中配置的远程仓库只是 Maven 用来找依赖的，而插件的远程仓库是内置的，一般可以直接在 [`repo1.maven.org/maven2`](http://repo1.maven.org/maven2) 中找到，要自己单独配置插件的远程仓库的话，需要通过 pluginRepositories→pluginRepository 命令进行配置。比如，下面的配置代码是默认插件仓库的配置。

```

<pluginRepositories>
    <pluginRepository>
        <id>central</id>
        <name>Maven Plugin Repository</name>
        <url>http://repo1.maven.org/maven2</url>
        <layout>default</layout>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
        <releases>
            <updatePolicy>never</updatePolicy>
        </releases>
    </pluginRepository>
</pluginRepositories>
```

如果在中央仓库中实在是找不到需要的插件，可以模仿上面的代码，配置自己的远程插件仓库。

#### 2\. 插件默认的 groupId

在使用插件或者在 pom 中配置插件的时候，如果使用的是 Maven 官方插件的话，是可以不指定 groupId 的，因为这些插件的 groupId 都是一样的，都是 org.apache.maven.plugins。

Maven 对于官方 groupId 允许不在配置文件中明确配置。也就是说，如果没有配置指定 groupId 的话，Maven 默认认为是 org.apache.maven.plugins。这样就可以简化部分的配置工作。

当然，为了保险起见，建议大家不要嫌麻烦，还是自己指定 groupId 比较直观些。

#### 3\. 解析插件的版本

同样的目的，为了简化插件的配置和使用，可以不指定插件的版本。

如果没有指定插件的版本，Maven 对版本处理的方式是：如果插件不属于核心插件范畴，Maven 会去检测所有仓库中的版本，最终会选择最新版本，而且这个最新版本不排除是快照版本。快照版本是有稳定性缺陷的。

#### 4\. 解析插件的前缀

前面提到过，为了简化对插件的调用，可以在命令行中使用前缀指明要执行的插件，现在解释一下 Maven 是如何根据插件的前缀找到真正的插件的。

插件前缀与 groupId:artifactId 是一一对应的。这种对应关系保存在仓库的元数据中，这样的元数据为 groupId/maven-metadata.xml。要准确地解析到插件，还需要解释一下这里的 groupId。

前面介绍过，目前绝大部分插件都是放在 [`repo1.maven.org`](http://repo1.maven.org) 和 [`repository.codehaus.org`](http://repository.codehaus.org) 中的，它们的 groupId 对应的是 org.apache.maven.plugins 和 org.-codehaus.mojo。

Maven 在解析插件仓库元数据的时候，会默认使用 org.apache.maven.plugins 和 org.codehaus.mojo 两个 groupId，也就是说，Maven 会自动检测 [`repo1.maven.org/maven2/org/apache/maven/plugins/maven-metadata.xml`](http://repository.codehaus.org/org/codehaus/mojo/maven-metadata.xml)和 [`repository.codehaus.org/org/codehaus/mojo/maven-metadata.xml`](http://repository.codehaus.org/org/codehaus/mojo/maven-metadata.xml) 中的元数据。

当然，也可以告诉 Maven 从其他仓库中查找，只要在 settings.xml 中做如下配置。

```

<settings>
    <pluginGroups>
        <pluginGroup>cn.com.demo.plugins</pluginGroup>
    </pluginGroups>
</settings>
```

这样配置后，Maven 不仅仅会检测 org/apache/maven/plugins/maven-metadata.xml、org/codehaus/mojo/maven-metadata.xml，还会检测 cn/com/demo/plugins/maven-metadata.xml。

那插件仓库数据的内容是什么样的呢？下面列出了 org.apache.maven.plugins 中 groupId 部分的内容。

```

<metadata>
    <plugins>
        <plugin>
            <name>Maven Clean Plugin</name>
            <prefix>clean</prefix>
            <artifactId>maven-clean-plugin</artifactId>
        </plugin>
        <plugin>
            <name>Maven Compiler Plugin</name>
            <prefix>compiler</prefix>
            <artifactId>maven-compiler-plugin</artifactId>
        </plugin>
        <plugin>
            <name>Maven Dependency Plugin</name>
            <prefix>dependency</prefix>
            <artifactId>maven-dependency-plugin</artifactId>
        </plugin>
        ...
    </plugins>
</metadata>
```

从上面的内容中可以发现，maven-clean-plugin 的前缀是 clean，maven-compiler-plugin 的前缀是 compiler，maven-dependency-plugin 的前缀是 dependency。

当 Maven 解析到 compiler:compile 命令后，它首先基于默认的 groupId 归并所有插件仓库的元数据 org/apache/maven/plugins/maven-metadata.xml。接着检查归并后的元数据，找到对应的 artifactId 为 maven-compiler-plugin。

接下来再结合当前元数据的 groupId 为 org.apache.maven.plugins。最后找到仓库中最新的 version，从而就可以得到一个插件的完整坐标信息。

如果在第一个 metadata.xml 中没有找到目标插件，就用同样的流程找其他的 metadata.xml，包括用户自己定义的 metadata.xml。如果所有的地方都没有找到对应的前缀，这就以报错的形式结束了。