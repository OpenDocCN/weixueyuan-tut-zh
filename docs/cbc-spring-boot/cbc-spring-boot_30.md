# 使用 SBT 构建和发布基于 SpringBoot 的 Scala 应用

> 原文：[`c.biancheng.net/view/4692.html`](http://c.biancheng.net/view/4692.html)

[SBT](http://start.spring.io) 是 Scala 生态圈里的经典构建工具，虽然很多人觉得 SBT 很复杂，还戏称其为 SB Tool，但其全称确是 Simple Build Tool。

实际上，很多产品（包括像 SBT 这样的工具和技术产品）只有一个打动用户的特性就够了， Triggered Execution 这一特性支持，就可以在一个屏幕上写代码，而在另一个屏幕（或者窗口）实时地获得编译结果反馈，如图 1 所示，参与感十足。
![结合双屏使用 SBT 的工作场景示意图](img/fb7c075031575f71da622ec68d71db3d.png)
图 1  结合双屏使用 SBT 的工作场景示意图
当然，SBT 毕竟是一套新的工具体系，与 Maven 或者其他构建工具在配置和使用上还是会有不小的差异，这就意味着，如果我们要使用 SBT 来构建基于 Scala 的 SpringBoot 微服务项目，就需要针对 SBT 的特点做很多定制工作。

## 探索基于 SBT 的 SpringBoot 应用开发模式

SpringBoot 团队围绕 Maven 提供了很多便利和支持，比如我们只要使用 Maven 的继承特性，将 spring-boot-starter-parent 作为当前项目的 parent，就可以直接开发 SpringBoot 微服务项目，代码如下所示：

```

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.3.2.RELEASE</version>
</parent> 
```

或者，如果当前要开发的 SpringBoot 微服务项目需要继承其他 parent 项目而不能使用 spring-boot-starter-parent，那么也可以使用 Maven 的依赖引入特性完成一系列 SpringBoot 相关依赖导入：

```

<dependencyManagement>
    <dependencies>
        <dependency> <!-- Import dependency management from Spring Boot -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>1.3.2.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement> 
```

当使用 SBT 时，这些围绕 Maven 提供的便利就不复存在了，我们需要搞清楚这些便利背后都发生了什么，才能最终决定如何使用 SBT 进行相应的配置并最终达到相同的目的。

因为 spring-boot-starter-parent 项目只有一个 pom.xml 定义，且其 parent 就是 spring-boot-dependencies，所以，我们先沿着 spring-boot-dependencies 开始寻找端倪。

但是我们发现，spring-boot-dependencies 也只是一个只有 pom.xml 定义的“干瘪”项目，其 pom.xml 中仅仅通过 dependencyManagement 和 pluginManagement 固定了 SpringBoot 项目的一系列依赖和版本号，并无实际有用信息，所以，我们需要回头再从 spring-boot-starter-parent 的 pom.xml 中寻找线索。

不幸的是，spring-boot-starter-parent 也没有提供什么有用的线索，它的 pom.xml 中也是定义了相应的 pluginManagement 来规范依赖和依赖的版本，并在编译期间进行相应资源和配置的过滤。

到了这个地步，我们就只能另寻他途了。不过，既然大部分的 SpringBoot 项目都会从依赖相应的 starter 项目开始，那么，我们可以分析几个 spring-boot-starter-XXX 模块来看看它们有什么共性。

假设我们关联查看了 spring-boot-starter-web、spring-boot-starter-jdbc、spring-boot-starter-actuator 等模块，就会发现，除了这些模块自身特定要提供的依赖，它们都同时依赖如下模块：

```

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency> 
```

而继续追踪下去到 spring-boot-starter 的 pom.xml 定义内部，最终才“柳暗花明又一村”：

```

...
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-logging</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <exclusions>
            <exclusion>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.yaml</groupId>
        <artifactId>snakeyaml</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
... 
```

所以，一个 SpringBoot 项目得以成型，如下依赖基本是必备的：

*   org.springframework.boot：spring-boot
*   org.springframework.boot：spring-boot-autoconfigure
*   org.springframework.boot：spring-boot-starter-logging
*   org.springframework：spring-core
*   org.yaml：snakeyaml

作为 SpringBoot 项目，spring-boot 依赖自然不可少。

*   spring-boot-autoconfigure 依赖可以帮助我们进行自动配置，SpringBoot 项目的便利的核心就在于此。
*   spring-boot-starter-logging 则配置默认的日志依赖。
*   SpringBoot 项目就是一个 Spring 项目，所以 org.springframework：spring-core 也是必备依赖。
*   org.yaml：snakeyaml，是运行期依赖，主要提供针对 yml 格式配置文件的支持。

至此，我们基本已经了解了 SpringBoot 项目背后的故事全貌，接下来就要介绍 SBT 了。

构建在 SPRING INITIALIZR 之上的 [`start.spring.io`](http://start.spring.io) 可以在每次创建新的 SpringBoot 项目的时候提供“脚手架”功能，创建 SBT 项目其实也有这样的“脚手架”工具，即 Typesafe Activator（[`www.typesafe.com/activator/download`](https://www.typesafe.com/activator/download)）。

我们使用 Typesafe Activator 来构建一个新的 SBT 项目，用于构建同样功能的一个汇率查询 Web API 微服务，Typesafe Activator 安装完成后执行如下命令：

$ activator new currency-webapi-with-scala-sbt minimal-scala

其中，currency-webapi-with-scala-sbt 为我们要生成的 SBT 项目名，而 minimal-scala 为我们选择要使用的“脚手架”模板项目名。

初始生成的 SBT 项目的构建文件内容如下：

```

name := """currency-webapi-with-scala-sbt"""
version := "1.0"scalaVersion:= "2.11.7"
// Change this to another test framework if you
preferlibraryDependencies += "org.scalatest" %
% "scalatest" % "2.2.4" %"test"
// Uncomment to use Akka
// libraryDependencies += "com.typesafe.akka" %
% "akka-actor" % "2.3.11"
```

我们需要对其进行定制和丰富一些内容：

```

organization := "com.keevol.springboot.chapter5"
name := """currency-webapi-with-scala-sbt"""
version := "1.0.0-SNAPSHOT"
scalaVersion := "2.11.7"
resolvers += "Local Maven Repository" at
"file://"+Path.userHome.absolutePath+"/.m2/repository"
libraryDependencies += "org.springframework.boot" % "spring-boot-starter-web" %
"1.3.2.RELEASE"
libraryDependencies += "com.keevol.springboot" % "currency-rates-service" % "1.0-SNAPSHOT"
// Change this to another test framework if you
preferlibraryDependencies += "org.scalatest" %
%"scalatest" %
"2.2.4" % "test" 
```

因为我们已经了解了 SpringBoot 项目依赖的传递关系，所以，现在只要将 spring-boot-starter-web 作为核心依赖配置到 build.sbt 中，就可以信心满满地着手开发了（毕竟 spring-boot-starter-web 的依赖会一路上接力传递，必需的基础依赖一个都不会少）。

除此之外，currency-rates-service 属于业务依赖，也需要一并遵循 SBT 的配置语法加以配置。因为实例项目是使用本地开发，所以，我们配置了相应的 resolvers 指引 SBT 从本地的 Maven 仓库中加载业务依赖。

实际情况下，大家可以根据需要，将自己公司内部的 Maven 远程仓库或者其他 Maven 仓库也加入进来。

SBT 的项目源码结构与 Maven 的项目源码结构约定基本相同，所以，在 build.sbt 中所有配置完成后，就可以在 src/main/scala 目录下开始编写 scala 代码了：

```

// CurrencyRateQueryController.scala 源码文件
package com.mengma.controllers
import com.mengma.WebApiResponse
import com.keevol.springboot.services.currency.rates.CurrencyPair
import com.keevol.springboot.services.currency.rates.CurrencyRateService
import com.keevol.springboot.services.currency.rates.ExchangeRate
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.web.bind.annotation.{RequestMethod, RequestMapping,
RestController}
@RestController
class CurrencyRateQueryController{
    @Autowired
    var currencyRateService: CurrencyRateService = _
    @RequestMapping(value = Array("/"), method = Array(RequestMethod.GET))
    def quote(symbol: String): WebApiResponse[ExchangeRate] = {
        val response: WebApiResponse[ExchangeRate] = new WebApiResponse [ExchangeRate]
        response.setCode(WebApiResponse.SUCCESS_CODE)
        response.setData(currencyRateService.quote(CurrencyPair.from (symbol)))
        response
    }
}
// CurrencyWebApiBootStrap.scala 源码文件
package com.mengma.springboot
import com.keevol.springboot.services.currency.rates.CurrencyRateRepository
import com.keevol.springboot.services.currency.rates.CurrencyRateService
import com.keevol.springboot.services.currency.rates.CurrencyRateServiceImpl
import org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.context.annotation.Bean
@SpringBootApplication
class CurrencyWebApiBootStrap{
    @Bean
    def currencyRateService:CurrencyRateService = {
        val service: CurrencyRateServiceImpl = new CurrencyRateServiceImpl
        service.setRateRepository(new CurrencyRateRepository) service
    }
}
object CurrencyWebApiBootStrap{
    def main(args: Array[String]) {
        SpringApplication.run(classOf[CurrencyWebApiBootStrap], args: _*)
    }
} 
```

开发完成后，运行 sbt run 或者直接通过 sbt"run-main com.keevol.springboot.chapter5.CurrencyWebApiBootStrap"即可启动微服务。

## 探索基于 SBT 的 SpringBoot 应用发布策略

针对我们基于 SBT 和 Scala 的 SpringBoot 微服务开发完成后，需要发布出去才能发挥其价值，要完成这种类型的项目发布，有几种策略可以考虑。

一种策略是使用 SpringBoot 推荐的可执行 jar 包发布形式，SpringBoot 团队提供的 spring-boot-maven-plugin 默认对这种可执行 jar 包有很好的支持。

但是，对于微服务来说，我们前面说了，不建议采用这种方式，除非零星个别的一些项目，不需要强大体系支撑，仅靠人工就可以满足或者忍受。

不过，如果因为某些因素必须使用 SpringBoot 建议的可执行 jar 包发布形式，那么，可以结合 spring-boot-maven-plugin 的源码和逻辑，并实现一个相应的 SBT 插件即可。

另一种策略是使用 SBT 生态圈中已经形成一定口碑或者说基本上已经是事实标准的方案，比如使用 sbt-native-packager（[`github.com/sbt/sbt-native-packager`](https://github.com/sbt/sbt-native-packager)），通过 sbt-native-packager 这个 SBT 插件，我们可以将当前 SBT 项目发布为 ZIP、TAR、RPM、DEB 甚至 docker 等形式，相对于自己开发一个 SBT 插件完成可执行 jar 包形式的发布，显然直接使用 sbt-native-packager 是更明智的做法。

针对我们基于 SBT 和 Scala 的汇率查询 SpringBoot 微服务，要使用 sbt-native-packager 进行发布，我们只要对项目的 build.sbt 添加少许发布指引就可以了：

```

import sbt._
import com.typesafe.sbt.packager.universal.UniversalPlugin.autoImport._
import com.typesafe.sbt.packager.MappingsHelper._lazy
val root = project in file(".") enablePlugins(JavaAppPackaging)
organization := "com.mengma.springboot"\
name := """currency-webapi-with-scala-sbt"""
version := "1.0.0-SNAPSHOT"
scalaVersion := "2.11.7"
resolvers += "Local Maven Repository" at "file://"+Path.userHome.absolutePath+"/.m2/repository"
libraryDependencies += "org.springframework.boot" % "spring-boot-starter-web" %
"1.3.2.RELEASE"
libraryDependencies += "com.keevol.springboot" % "currency-rates-service" % "1.0-SNAPSHOT"
// Change this to another test framework if you prefer
libraryDependencies += "org.scalatest" % %"scalatest" % "2.2.4" % "test"
// 发布相关 main
Class in Compile := Some("com.mengma.springboot.CurrencyWebApiBootStrap") mappings
in Universal += file("LICENSE") ->
"LICENSE"mappings in Universal ++= directory("config")
```

我们首先通过 import 引入相应的依赖，然后声明当前项目以 Java 应用（JavaAppPackaging）的形式发布（不是 Scala 应用类型是因为编译完运行期都是 Java 字节码的形式），最后通过 mainClass 来指定启动类是哪一个，以及应该将哪些其他文件或者目录打入发布包之中。

在执行发布命令之前，我们还有最后一步要做，就是将 sbt-native-packager 添加为当前项目的依赖，在 SBT 项目结构下，这需要我们在当前项目根目录下新建 project/plugins.sbt 文件，然后添加如下内容：

```

resolvers += "Typesafe repository" at
"http://repo.typesafe.com/typesafe/releases/"resolvers +=
Resolver.url("fix-sbt-plugin-releases",
url("https://dl.bintray.com/sbt/sbt-plugin-releases"))(Resolver.ivyStylePatterns)addMavenResolverPluginaddSbtPlugin("com.typesafe.sbt"
% "sbt-native-packager" % "1.0.0")
```

之后，build.sbt 才可以依赖到 sbt-native-packager 并且编译成功，现在，只要我们运行 sbt universal：packageBin 命令，就可以在 target/universal 目录下获得一个 zip 形式的发布安装包 currency-webapi-with-scala-sbt-1.0.0-SNAPSHOT.zip，安装包格式包含以下内容：

*   自动生成启动脚本的 bin 目录。
*   存放了项目所有依赖的 lib 目录。
*   以及在 build.sbt 中通过 mapping 添加的一系列希望打包的文件和目录。

当然，zip 不是微服务发布的理想形式，但基于 RPM 和 DEB 等发布包需要依赖复杂的本地环境准备，故此，这里为了示例的简化，使用 zip 发布形式来说明如何使用 sbt-native-packager，以轻松愉快的心情完成基于 SBT 和 Scala 的 SpringBoot 项目发布。

关于如何准备 RPM、DEB 等发布包的编译环境，以及如何配置 sbt-native-packager 使之相应的将当前项目发布为 RPM、DEB 等形式，可以参考 sbt-native-packager 插件的网站文档（[`www.scala-sbt.org/sbt-native-packager/index.html`](http://www.scala-sbt.org/sbt-native-packager/index.html)）。

最后一种策略（或许还有）我认为是最适合微服务的终极发布策略，即外部化（Externalization）的发布策略。

无论是在 Maven 项目的 pom.xml 中定义和使用 spring-boot-maven-plugin，还是在 SBT 项目的构建配置中定义和使用 sbt-native-packager 插件，本质上都是一种将通用逻辑分散到一个个单独项目中的做法，如果只是小团队以及少量项目，这样做是没有太大问题的。

而一旦规模膨胀（对于微服务来说，数量、发布频度等指标规模膨胀很正常），加上人员流动、时机错配等一系列的变化因素，这种通用逻辑最终很可能会在这些一个个离散的项目中被“玩坏”，造成发布的成品千差万别，完全背离标准化的微服务这一理想轨道，进而带来整体微服务交付链路上的一系列不必要的负累。

所以，为了避免“该标准化的地方却使用个性化”所可能引发的一系列问题，我们应该将这些发布和部署逻辑从单一项目中剥离出来，即外部化掉（Externalize it），将这些逻辑以发布和部署平台的形式抽象和固化，从而将原来因为各种因素导致的不确定性最大化地转变成确定性。

发布和部署平台是外部化的最终成果，每个 SpringBoot 微服务项目，不管你使用的是什么语言开发，也不管你使用的什么构建工具，只要你想用并且能帮助你提高开发效率，在开发阶段尽管用即可。

一旦项目要发布，直接将发布请求对接发布和部署平台。对于 SpringBoot 项目的开发者来说，发布和部署平台是访问接口（Interface）；而对于发布和部署平台的开发者来说，则是服务提供方。服务提供方可以灵活替换和升级：

如果你的 SpringBoot 项目是使用 Maven 作为构建工具，那么，发布和部署平台实现层会自动感知并构建发布。

如果你的 SpringBoot 项目是使用 SBT 作为构建工具，那么，发布和部署平台实现层也会自动感知并构建发布。

如果组织内部当前的微服务体系是基于 RPM 标准统一发布形式，那么，所有 SpringBoot 项目不管你使用什么构建工具，都将以 RPM 标准形式发布。

如果组织内部要实现 DevOps 体系的升级换代（比如 docker），那么，只要发布和部署平台实现层进行变更和升级即可，SpringBoot 项目的开发者无须感知，发布和部署平台起到了很好的抽象和防火墙效果。

所以，对于一个拥有成熟微服务体系的组织来说，将发布等通用逻辑外部化到发布和部署平台，应该是最合适，也是目前最终极的策略。