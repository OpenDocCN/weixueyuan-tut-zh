# Maven 仓库的分类

> 原文：[`c.biancheng.net/view/4985.html`](http://c.biancheng.net/view/4985.html)

Maven 存放构件的仓库分两种：本地仓库和远程仓库。Maven 寻找构件的时候，先查看本地仓库，如果本地仓库存在坐标对应的构件，就直接使用。

如果本地仓库不存在所需要的构件，或者需要查看是否有更新的构件版本，Maven 就会去远程仓库查找，发现需要的构件后，下载到本地仓库后使用。如果本地仓库和远程仓库都没有找到需要的构件，Maven 就报错。

远程仓库又可以分为三种：一种是中央仓库；另一种是私服；还有一种就是其他公共仓库。

中央仓库是 Maven 自带的远程仓库，它包含了绝大部分开源的构件。在默认配置下，当本地仓库没有 Maven 需要的构件的时候，都会尝试从中央仓库下载。

私服是另外一种特殊的远程仓库。为了节省带宽，节约下载构件的时间，在局域网内架设一个私有的仓库服务器用来代理所有的外部远程仓库。局域网里面的项目还能部署到私服上供其他项目使用。

除了中央仓库和私服外，还有很多其他公开的远程仓库，常见的有 java.net Maven 库（[`download.java.net/maven/2/2`](http://download.java.net/maven/2/2)）和 JBoss Maven 库（[`repository.jboss.com/maven2/`](http://repository.jboss.com/maven2/)）。

为了更具体地增加对仓库的理解，这里详细介绍一下各种仓库。

#### 1\. 本地仓库

Maven 在根据坐标查找依赖的构件时，先是在本地仓库中查找。默认情况下，不管是 Windows 操作系统还是 Linux 操作系统，每个用户在自己的用户目录下都有一个路径名为 .m2/repository/ 的目录，这个目录就是 Maven 的本地仓库目录。比如，笔者的用户名是 Noble，计算机上的默认本地仓库的目录就是 C:\Users\Noble\.m2\repository\。

一般为了便于文件的管理，用户会希望自定义本地仓库的目录。可以编辑 ~/.m2/settings.xml 文件，设置其中的 localRepository 元素的值，就可以改变 Maven 本地仓库的默认位置。例如：

<settings>
    ...
    <localRepository>
        C:/java/servers/apache-archiva-2.2.1/repositories/internal
    </localRepository>
    ...
</settings>

这样，本地仓库的目录就是 C:/java/servers/apache-archiva-2.2.1/repositories/internal 了。

另外需要注意一下的是，默认情况下，~/.m2/settings.xml 文件是不存在的，需要从 Maven 的安装目录中复制 ＄M2_HOME/conf/settings.xml 文件到 ~/.m2/ 目录下，再进行编辑。

当然如果嫌麻烦的话，可以直接修改 ＄M2_HOME/conf/settings.xml 文件，效果也是一样的。但是不建议修改，因为 Maven 目录下的 settings.xml 是全局的，也就是每个用户都共享，而 ~/.m2/settings.xml 只是对当前用户起作用，修改后不会影响其他用户。

一个构件只有存在本地仓库后才能被 Maven 项目使用。将构件保存到本地仓库最常见的有两种方式，一种是以依赖的形成，从远程仓库下载到本地仓库；另一种是将本地项目编译打包后，安装到本地仓库。

#### 2\. 远程仓库

安装好 Maven 后，如果不执行任何 Maven 命令的话，本地仓库目录是不存在的。当用户输入第 1 条 Maven 命令后，Maven 才会创建本地仓库。然后根据配置和需要从远程仓库下载对应的构件到本地仓库，以备需要的时候使用。

本地仓库只会有一个，而远程仓库可以有很多。

#### 3\. 中央仓库

由于最原始的本地仓库是空的，Maven 必须知道至少一个远程仓库才能执行 Maven 的命令。这个远程仓库是默认的，也就是不需要用户专门配置，这里把它叫作中央仓库。也就是说，中央仓库就是一个默认的远程仓库。

用户可以打开 ＄M2_HOME/lib/maven-model-builder-3.3.9.jar，其中有 org/apache/maven/model/pom-4.0.0.xml 文件（注意 jar 文件的 3.3.9 是当前 Maven 的版本号，要根据自己的版本号找对应的 jar 文件），其中有如下内容。

```

<project>
    <modelVersion>4.0.0</modelVersion>

    <repositories>
        <repository>
            <id>central</id>
            <name>Central Repository</name>
            <url>https://repo.maven.apache.org/maven2</url>
            <layout>default</layout>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>central</id>
            <name>Central Repository</name>
            <url>https://repo.maven.apache.org/maven2</url>
            <layout>default</layout>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <releases>
                <updatePolicy>never</updatePolicy>
            </releases>
        </pluginRepository>
    </pluginRepositories>
    ...
</project>
```

中间 repository 配置的是依赖的默认中央仓库，pluginRepository 配置的是插件的默认中央仓库。很凑巧，它们都是 [`repo.maven.apache.org/maven2`](https://repo.maven.apache.org/maven2)。

所有的 Maven 项目都会继承这个 pom.xml，所以通常把这个 pom 叫超级 pom，与 Java 中的 Object 类一样，也是所有的类都自动继承 Object 类，而 Object 类也叫超级类。

中央仓库包含了这世界上绝大多数流行的开源 Java 构件，以及对应的源代码、作者信息、scm、信息许可证等，每天都接受全世界 Java 程序员无数次访问。可以想象没有它，Java 程序员的世界将变得多么黑暗。

由于中央仓库中包含了差不多所有流行的构件，所以一个简单的 Maven 工程所需要的依赖构件都可以直接从中央仓库中找到并且下载下来。所以用户安装好 Maven 后，基本上不用做太多的额外配置，就可以直接开发 Maven 项目。

#### 4\. 私服

私服是一个特殊的远程仓库，架设在局域网内。它是一个代理外网的远程仓库，供局域网内部的 Maven 用户使用。

当局域网内部的 Maven 用户需要构件的时候，先是从自己的本地仓库中查找，没有找到，就在私服上面查找，还没找到，就从外部的远程仓库查找并下载。这时候需要注意一下，没有私服的时候，Maven 是直接把从外部远程仓库下载的构件保存到本地仓库中。

现在有私服了，从外部远程仓库下载的构件，会先保存一份在私服，再在 Maven 用户的本地仓库中保存一份。保存在 Maven 用户本地仓库中的构件，可以实现一个用户的多个 Maven 工程共享使用，保存在私服中的构件，可以实现多个 Maven 用户在局域网内共享使用。这样做，有如下几个方面的优势。

#### 1）节省外部带宽

建立私服可以减少 Maven 开发团队的开支。大量的对外部仓库的重复请求会浪费太多的网络流量。

利用私服代理后，每个构件只要消耗一次必需的流量，局域网中任何一个 Maven 用户要使用某个已经使用过的构件时，就不需再请求远程仓库，只需从私服中直接获取就行了。

#### 2）提高 Maven 的效率

不停地请求外面仓库下载构件，不仅会浪费流量，还很耗时，特别是网速比较慢的时候。搭建私服就不用频繁请求外部远程仓库了，而且局域网的速度一般要比外网速度快很多。

#### 3）便于部署第三方构件

如果某个构件无法从外部远程仓库获得，没有私服相对会比较麻烦。如果有了私服，可以将它们部署到私服中，这样局域网内的 Maven 用户就可以找到对应的构件了。

比如 Oracle 驱动程序，因为版权没有放开，在外部远程仓库中是没有部署的，就可以在自己局域网内部的私服中进行部署。这样局域网内部的 Maven 用户就可以使用 Oracle 驱动程序构件了，而且也不会影响 Oracle 的版权。

#### 4）提高 Maven 的稳定性，更方便控制管理

Maven 构件如果高度依赖远程仓库的话，一旦外网不稳定，比如中断，就会直接影响 Maven 项目。使用了私服后，因为私服中已经保存了大量的构件，即使外网断了，只要当前的构建任务中没有使用到一个以前构建没有用过的构件，都可以照常工作。

另外，一些私服软件还提供了很多额外的管理功能：比如权限管理、RELEASE/SNAPSHOT 区分等。

#### 5）降低中央仓库的负荷压力

搭建私服能降低中央仓库的压力，让中央仓库更好地为那些真正需要的用户服务。自己少请求一次中央仓库，就给别人留出一次快速请求的机会。

通过《Maven 私服的配置和使用》教程可以知道，在一支团队开发中 Maven 私服是一个重要的环境。《Apache 基金会的 Archiva 服务器的搭建步骤》专门以 Archiva 为例，介绍如何搭建 Archiva 私服。用户现在可以复习以前的操作步骤，深入理解私服的意义。