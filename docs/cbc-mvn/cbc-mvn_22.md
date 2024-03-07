# Maven 坐标详解

> 原文：[`c.biancheng.net/view/4955.html`](http://c.biancheng.net/view/4955.html)

通过教程前面内容的学习和练习，用户可以发现要完成一个项目的开发和构建，总是要使用构件，而且这些构件已经被 Maven 仓库管理好了。不管是在本地仓库、私服还是远程仓库、中央仓库中，总之，就是被仓库管理了。

那 Maven 是通过什么方式精确地找到用户想要的构件呢？

其实，前面已经介绍过，那就是通过构件的坐标去唯一定位查找。反过来也就是说，在 Maven 仓库中，是用坐标标记来一一对应地管理每个构件的。

那坐标又是由哪些信息组成的呢？

一个完整的坐标信息，由 groupId、artifactId、version、packaging、classifier 组成，如下是一个简单的坐标定义。

<groupId>org.SpringFramework</groupId>
<artifactId>spring-core</artifactId>
<version>4.2.7.RELEASE</version>
<packaging>jar</packaging>

这是 JUnit 的坐标，下面详细介绍一下各个元素。

#### 1\. groupId

定义当前 Maven 项目从属的实际项目。关于 groupId 的理解如下所示。

1）Maven 项目和实际项目不一定是一一对应的。比如 SpringFramework，它对应的 Maven 项目就有很多，如 spring-core、spring-context、spring-security 等。造成这样的原因是模块的概念，所以一个实际项目经常会被划分成很多模块。

2）groupId 不应该同开发项目的公司或组织对应。原因比较好理解，一个公司和一个组织会开发很多实际项目，如果用 groupId 对应公司和组织，那 artifactId 就只能是对应于每个实际项目了，而再往下的模块就没法描述了，而往往项目中的每个模块是以单独的形式形成构件，以便其他项目重复聚合使用。

3）groupId 的表述形式同 Java 包名的表述方式类似，通常与域名反向一一对应。

#### 2. artifactId

定义实际项目中的一个 Maven 项目（实际项目中的一个模块）。

推荐命名的方式为：实际项目名称-模块名称。

比如，org.springframework 是实际项目名称，而现在用的是其中的核心模块，它的 artifactId 为 spring-core。

#### 3. version

定义 Maven 当前所处的版本。如上的描述，用的是 4.2.7.RELEASE 版本。需要注意的是，Maven 中对版本号的定义是有一套规范的。具体规范请参考《版本管理》的介绍。

#### 4\. packaging

定义 Maven 项目的打包方式。

打包方式通常与所生成的构件文件的扩展名对应，比如，.jar、.ear、.war、.pom 等。另外，打包方式是与工程构建的生命周期对应的。比如，jar 打包与 war 打包使用的命令是不相同的。最后需要注意的是，可以不指定 packaging，这时候 Maven 会自动默认成 jar。

#### 5\. classifier

定义构件输出的附属构件。

附属构件同主构件是一一对应的，比如上面的 spring-core-4.2.7.RELEASE.jar 是 spring-core Maven spring-core 项目的主构。

Maven spring-core 项目除了可以生成上面的主构件外，也可以生成 spring-core-4.2.7.RELEASE-javadoc.java 和 spring-core-4.2.7.RELEASE-sources.jar 这样的附属构件。这时候，javadoc 和 sources 就是这两个附属构件的 classifier。这样就为主构件的每个附属构件也定义了一个唯一的坐标。

最后需要特别注意的是，不能直接定义一个 Maven 项目的 classifier，因为附属构件不是由 Maven 项目构建的时候直接默认生成的，而是由附加的其他插件生成的。

前面介绍的组成坐标的 5 个要素中，groupId、artifactId 和 version 是必需的，packaging 是可选的，默认是 jar，而 classifier 是不能直接定义的。同时，Maven 项目的构件文件名与坐标也是有对应关系的，一般规则是 artifactId-version[-classifier].packaging。