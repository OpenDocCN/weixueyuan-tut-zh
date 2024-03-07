# Maven 依赖配置和依赖范围

> 原文：[`c.biancheng.net/view/5005.html`](http://c.biancheng.net/view/5005.html)

教程前面用坐标一一对应地描述了构件，并且保存在仓库中了。那用坐标描述好后，把它们放在仓库中的作用是什么呢？

当其他项目需要在这些构件的基础上做开发的时候，用户就没必要自己再重新实现一遍了。直接指定坐标，告诉 Maven 将坐标对应的构件从仓库中找出来，集成到新项目中就可以了。这时候引入的构件，就是新项目的依赖。

依赖一般分以下两个层次理解：

1）在 Maven 项目的 pom.xml 中配置所需要构件的坐标，也就是配置依赖。还有就是 Maven 在构建项目的时候，根据坐标从仓库中找到坐标所对应的构件文件，并且把它们引入 Maven 项目中来，也就是 Maven 引用。

2）由 Maven 构建的时候自己搞定。前面也介绍了 Maven 基于坐标寻找要执行的插件的思路。实际上，插件本身就是一个特殊的构件。查找插件的思路也就是依赖查找的思路。这里需要把握的更多的是第一层次，即怎样配置依赖，以及指定依赖内部的关系和优化等。

## 依赖的配置

掌握依赖，从配置开始。接下来介绍一下依赖的配置。依赖是配置在 pom.xml 中的，如下是关于依赖配置的大概内容：

```

<project>
    ...
    <dependencies>
        <dependency>
            <groupId>...</groupId>
            <artifactId>
                ...
            </artifactId>
            <version>...</version>
            <type>...</type>
            <scope>...</scope>
            <optional>...</optional>
            <exclusions>
                <exclusion>...</exclusion>
            </exclusions>
        </dependency>
        ...
    </dependencies>
    ...
</project>
```

通过前面依赖配置样例会发现，依赖配置中除了构件的坐标信息、groupId、artifactId 和 version 之外，还有其他的元素。接下来就简单介绍一下这些元素的作用。

*   groupId、artifactId 和 version：依赖的基本坐标。对于任何依赖，基本坐标是最基本、最重要的，因为 Maven 是根据坐标找依赖的。
*   type：依赖的类型，同项目中的 packaging 对应。大部分情况不需要声明，默认是 jar。
*   scope：依赖的范围，详细情况后面介绍。
*   optional：标记依赖是否可选，详细情况后面介绍。
*   exclusions：排除传递性依赖，详细情况后面介绍。

## 依赖的范围

Java 中有个环境变量叫 classpath。JVM 运行代码的时候，需要基于 classpath 查找需要的类文件，才能加载到内存执行。

Maven 在编译项目主代码的时候，使用的是一套 classpath，主代码编译时需要的依赖就添加到这个 classpath 中去；Maven 在编译和执行测试代码的时候，又会使用一套 classpath，这个动作需要的依赖就添加到这个 classpath 中去；Maven 项目具体运行的时候，又有一个独立的 classpath，同样运行时需要的依赖，肯定也要加到这个 classpath 中。这些 classpath，就是依赖的范围。

依赖的范围，就是用来控制这三种 classpath 的关系（编译 classpath、测试 classpath 和运行 classpath），接下来分别介绍依赖的范围的名称和意义。

#### 1）compile

编译依赖范围。如果在配置的时候没有指定，就默认使用这个范围。使用该范围的依赖，对编译、测试、运行三种 classpath 都有效。

#### 2）test

测试依赖范围。使用该范围的依赖只对测试 classpath 有效，在编译主代码或运行项目的时候，这种依赖是无效的。

#### 3）provided

已提供依赖范围。使用此范围的依赖，只在编译和测试 classpath 的时候有效，运行项目的时候是无效的。比如 Web 应用中的 servlet-api，编译和测试的时候就需要该依赖，运行的时候，因为容器中自带了 servlet-api，就没必要使用了。如果使用了，反而有可能出现版本不一致的冲突。

#### 4）runtime

运行时依赖范围。使用该范围的依赖，只对测试和运行的 classpath 有效，但在编译主代码时是无效的。比如 JDBC 驱动实现类，就需要在运行测试和运行主代码时候使用，编译的时候，只需 JDBC 接口就行。

#### 5）system

系统依赖范围。该范围与 classpath 的关系，同 provided 一样。但是，使用 system 访问时，必须通过 systemPath 元素指定依赖文件的路径。因为该依赖不是通过 Maven 仓库解析的，建议谨慎使用。

如下代码是一个使用 system 范围的案例。

```

<dependency>
    <groupId>xxx</groupId>
    <artifactId>xxx</artifactId>
    <version>xx</version>
    <scope>system</scope>
    <systemPath>e:/xxxx/xxx/xx.jar</systemPath>
</dependency>
```

#### 6）import

导入依赖范围。该依赖范围不会对三种 classpath 产生实际的影响。它的作用是将其他模块定义好的 dependencyManagement 导入当前 Maven 项目 pom 的 dependencyManagement 中。比如有个 SpringPOM Maven 工程，它的 pom 中的 dependencyManagement 配置如下：

```

<project>
    ...
    <groupId>cn.com.mvn.pom</groupId>
    <artifactId>SpringPOM</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>pom</packaging>
    ...
    <dependencyManagement>
        <dependencies>
            <!-- spring -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${project.build.spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aop</artifactId>
                <version>${project.build.spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>${project.build.spring.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    ...
</project>
```

接下来创建一个新的 Maven 工程 Second，要将 First 工程中 pom 中定义的 dependency-Management 原样合并过来，除了复制、继承之外，还可以编写如下代码，将它们导入进去。

```

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>cn.com.mvn.pom</groupId>
            <artifactId>SpringPOM</artifactId>
            <version>0.0.1-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## 传递性依赖

在使用 Maven 之前，如果要基于 Spring 框架开发项目，除了要加入 Spring 框架的 jar 包外，还需要将 Spring 框架所用到的第三方 jar 包加入。否则编译通过，但是运行的时候就会出现 classNotFound 异常。

为了解决这种问题，一般有两种方式：一种是下载 Spring 的 dependencies.zip 包，将其中的所有 jar 包都导入工程；另一种是根据运行时的报错信息，确定哪些类没有，再将包含这些类的 jar 包下载下来导入。

第一种方式虽然可以一次性解决所有需要 jar 包的导入问题，但是当查看工程的 jar 包会发现，有不少多余的 jar 包。这些多余的 jar 包不仅仅加大了项目的体积，还有可能同其他框架所导入的 jar 包有版本冲突。

第二种方式虽然不会有多余的 jar 包存在，但是要根据每次启动的错误，一个个找到 jar 包，再导入。想象如果有 10 个 jar 包，就要启动 10 次，查看 10 次错误分别导入，有多麻烦。

Maven 的传递依赖机制就能解决这样的问题。

当项目基于 Spring 框架实现的时候，只需将 Spring 的依赖配置到 pom 的依赖元素就行。至于 Spring 框架所依赖的第三方 jar 包，用户不用处理，Maven 自己通过检测 Spring 框架的依赖信息将它们导入项目中来。而且只会导入 Spring 框架所需要的，不会导入多余的依赖。

也就是说，Maven 会解析项目中的每个直接依赖的 pom，将那些必要的间接依赖以传递依赖的形式引入项目中。

当然，传递依赖在将间接依赖引入项目的过程中也有它自己的规则和范围。这个规则和范围是同前面介绍的依赖范围紧密关联的。

现在有三个项目（A、B 和 C 项目），假设 A 依赖 B，B 依赖 C，这样把 A 对 B 的依赖叫第一直接依赖，B 对 C 的依赖叫第二直接依赖，而 A 对 C 的依赖叫传递依赖（通过 B 传递的）。

中间 A 到 B 第一直接依赖的范围和 B 到 C 第二直接依赖的范围，就共同决定了 A 到 C 的传递依赖范围。它们的影响效果，就如表 1 所示。

坐标第一列表示第一直接依赖的范围，第一行表示第二直接依赖的范围，中间的交叉点为共同影响后的传递依赖的范围。

表 1 依赖的传递

| 依赖 | compile | test | provided | runtime |
| Compile | compile | -- | -- | runtime |
| test | test | -- | -- | test |
| provided | provided | -- | provided | provided |
| runtime | runtime | -- |  -- | runtime |

通过前面的表格，可以得出如下规律。

*   当第二直接依赖为 compile 的时候，传递依赖同第一直接依赖一致。
*   当第二直接依赖为 test 的时候，没有传递依赖。
*   当第二直接依赖为 provided 的时候，值将第一直接依赖中的 provided 以 provided 的形式传递。
*   当第二直接依赖为 runtime 的时候，传递依赖的范围基本上同第一直接依赖的范围一样，但 compile 除外，compile 的传递依赖范围为 runtime。

## 依赖的调解

在使用 Maven 自动提供的传递依赖后，可以解决对应的依赖管理，特别是间接依赖管理中遇到的问题。但是，当多个直接依赖都带来了同一个间接依赖，而且是不同版本的间接依赖时，就会引起重复依赖，甚至包冲突的问题。

那么，Maven 在传递依赖的时候是按什么规则来的呢？

#### 1\. 依赖调解原则

Maven 依赖调解原则有两个：一个是路径优先原则；另一个是声明优先原则。当路径优先原则搞不定的时候，再使用声明优先原则。

比如有个项目 A，它有两个依赖：A→B→C→T（1.0），A→D→T（2.0）。会发现，A 最终对 T（1.0）和 T（2.0）都有间接依赖。这时候 Maven 会自动判断它的路径，发现 T（2.0）的路径长度为 2，T（1.0）的路径长度为 3，以最短路径为原则，将 T（2.0）引入当前项目 A。

如果有个项目 A，它有两个依赖：A→B→T（1.0），A→C→T（2.0）。这时候两条路径都是一样的长度 2，那 Maven 到底把哪个引入项目 A 呢？这时候 Maven 会判断哪个依赖在 pom.xml 中先声明，选择引入先声明的依赖。

#### 2．可选依赖

在实际项目中，存在一些比较特殊的依赖。比如数据访问层模块对数据库驱动的依赖就比较特殊了。DAO 层要访问数据库的时候，需要加入数据库驱动依赖，而且不同数据库驱动依赖是不一样的。如果在设计 DAO 层的时候，是按跨数据库标准实现的，这就引出了一个新问题，是在 pom.xml 中配置 MySQL 驱动依赖呢？还是配置 Oracle 驱动依赖？或者两个都配置？

其实仔细想想，前面三种选项都不合适。单独配置 MySQL 或 Oracle，这样就不能跨数据库了。两个数据库都配置，驱动之间就会有冲突，或有多余的依赖。

这时候，就直接把这两个数据库驱动的依赖都设置成可选依赖，代码如下：

```

<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.34</version>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>oracle</groupId>
        <artifactId>ojdbc14</artifactId>
        <version>10.2.0.4</version>
        <optional>true</optional>
    </dependency>
</dependencies>
```

在应用项目中再具体指定使用哪个依赖，例如：

```

<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.34</version>
    </dependency>
</dependencies>
```

需要说明的是，在实际项目中建议不要使用可选依赖。虽然可选依赖满足了对一个模块的特征多样性，同时还提供了更多的选择，但是在实际配置中，好像不仅没有减少配置代码，还增多了重复复制的机会。

同时从面向对象分析和设计的思路来说，也是建议遵循单一职责原则，也就是一个类只有一个功能，不要糅合太多的功能，这样不方便理解、开发和维护。

所以实际项目中，一般对不同数据库的驱动单独创建一个 Maven 工程。其他项目需要基于哪个数据库进行操作的话，引用对应的 Maven 的工程以来就行，用传递依赖引入需要的数据库驱动依赖。