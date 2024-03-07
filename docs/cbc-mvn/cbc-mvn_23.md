# Maven 仓库及管理方式介绍

> 原文：[`c.biancheng.net/view/4982.html`](http://c.biancheng.net/view/4982.html)

根据前面的样例可以知道，坐标和依赖是构件在 Maven 中的一个标记，而构件的真正存在的形式是文件，Maven 是通过仓库来统一管理这些文件的。接下来详细介绍一下 Maven 仓库。

## Maven 仓库的定义

在 Maven 中，所有的依赖、插件以及 Maven 项目构建完的输出都是以构件的形式存在的，都叫构件。任何一个构件都是由一组坐标信息唯一标识的。

在一台用于项目开发的计算机中有可能存在很多 Maven 项目，比如前面介绍的那么多样例代码，它们都是分布在不同的 Maven 项目中的。

这些 Maven 项目肯定都会用到 compiler 插件，除了这个插件外，还有很多特有的构件。比如，MvnSSHDemo.Struts 中就用到了 struts2 构件，MvnSSH.Spring 中就用到了 Spring 相关的构件，MvnSSM.SpringMVC 中用到了 Spring-web 构件等。而且这些直接依赖中，又会引入很多间接依赖，中间也肯定有交叉的构件依赖在引用。

如果同以前的开发模式一样，将各自用到的依赖对应的构件文件都体现到自己的对应目录，比如 lib 目录下的话，就会发现同样的文件会在很多工程里面重复存在。这样不仅造成了大量的磁盘空间浪费，也不便于统一管理。

所以 Maven 使用坐标机制来解决这个问题。它是怎么解决的呢？

Maven 统一存储了所有 Maven 项目用到的构件，这些构件都是共享的。当某个 Maven 项目要使用某些构件的时候，就直接通过构件的坐标引用共享的构件，不需要复制到每个 Maven 工程的独立物理目录中去。

这个统一的位置就叫仓库。Maven 的仓库，实际上就是 Maven 构件的公共仓库。在实际的 Maven 项目中，只需指明这些依赖的坐标，需要的时候（编译、测试、运行），由 Maven 自动根据坐标找到对应的构件后使用。

为了完全实现重用，Maven 项目构建完毕后生成的构件也可以安装或部署到仓库中，供其他 Maven 项目使用。

## 仓库的管理方式

前面介绍过，构件最终是以文件的形式存在的。既然是以文件的形式存在，那就少不了目录。

Maven 仓库其实就是使用固定规则的目录结构，把公共的构件保存在一个固定的存储位置，需要的时候可以按照规则找到这些具体的构件文件。这些规则与 pom.xml 中依赖的坐标是什么关系呢？

下面就以一个具体的例子来分析构件在仓库中保存的目录结构规则，以及它与坐标的关系。

比如有一个这样的构件：groupId＝cn.com.mvn.demo、artifactId＝Mvn-SSMDemo.SpringMVC、version＝0.0.1-SNAPSHOT、classifier＝jdk17、packaging＝jar，它对应的路径按如下步骤，逐个生成。

1）生成 groupId 对应的第一部分路径：将 groupId 中的点分隔符转换成路径分隔符。该例子中，groupId＝cn.com.mvn.demo 转换成 cn/com/mvn/demo/ 目录。

2）生成 artifactId 对应的第二部分路径：在第一部分路径的基础上，加一层同 artifactId 名称对应的一样的目录。

比如，例子中的 artifactId＝MvnSSMDemo.SpringMVC，加上前面的 groupId 对应的目录，最后的结果是 cn/com/mvn/demo/MvnSSMDemo.SpringMVC。

3）生成 version 对应的第三部分路径：同 artifactId 的转换类似，直接在前面的基础上追加同 version 同名的目录。

比如，例子中 version＝0.0.1-SNAPSHOT，生成的目录就是 cn/com/mvn/demo/MvnSSMDemo.SpringMVC/0.0.1-SNAPSHOT。到这一步，构件文件所在的目录基本形成。

4）前面 3 步，根据 groupId、artifactId 和 version，生成了构件文件在仓库目录下的子目录，接下来就开始按规则生成构件的文件名。

构件的文件名首先是由 artifactId 和 version 按 <artifactId>-<version> 组成第一部分。比如，例子中的文件名第一部分就是 MvnSSMDemo.SpringMVC-0.0.1-SNAPSHOT。

5）如果构件有 classifier 的话，在第（4）步生成的文件名的基础上，用连接符“-”再连接 classifier 名字，例子的文件名变成：MvnSSMDemo.SpringMVC-0.0.1-SNAPSHOT-jdk17。

6）最后再判断构件的 packaging。在前面的文件名称后面添加 packaging 的值对应的后缀名。比如本例，最后生成的目录和文件名是 cn/com/mvn/demo/MvnSSMDemo.SpringMVC/0.0.1-SNAPSHOT/MvnSSMDemo.SpringMVC-0.0.1-SNAPSHOT-jdk17.jar。

到现在为止，本节就介绍完了 Maven 是按什么规律将坐标信息转换成构件目录和文件名，最终保存在仓库中的。明白这规律后，Maven 根据坐标从仓库中寻找构件就只是前面生成构件目录和文件名的一个逆过程了。

其实，用户也可以按这样的规律在自己的本地仓库或自己的私服仓库中，找到对应坐标的具体构件的文件。