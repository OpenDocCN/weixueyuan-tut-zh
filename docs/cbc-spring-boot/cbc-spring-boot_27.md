# 使用 Maven 构建和发布基于 SpringBoot 的 Scala 应用

> 原文：[`c.biancheng.net/view/4683.html`](http://c.biancheng.net/view/4683.html)

使用 Maven 来构建 Scala 开发的 SpringBoot 项目，其实与使用 Maven 构建 Java 开发的 SpringBoot 项目很接近，差异的地方很少，主要有几个地方需要改动：

1）因为 Scala 语言需要使用自己的编译器（Compiler）进行编译，所以，我们需要在 Maven 构建过程中使用一个编译 Scala 代码的 Maven 插件。

Scala 项目在编译期间需要依赖 Scala 的 compiler 类库，所以，也需要将 org.scala-lang：scala-compiler 添加为 Maven 构建过程中使用的依赖。

2）Scala 应用运行期间需要依赖 org.scala-lang：scala-library，故此也同样需要加入到 Maven 项目依赖中。

首先通过 [`start.spring.io`](http://start.spring.io) 构建 currency-webapi-with-scala“脚手架”项目（Scaffolding），默认选择生成 Maven 项目（Maven Project），初始的 pom.xml 大致如下：

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.keevol.springboot.chapter5</groupId>
    <artifactId>currency-webapi-with-scala</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>currency-webapi-with-scala</name>
    <description>Demo project for Spring Boot and Scala with Maven </description>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.1.RELEASE</version>
        <relativePath /> <!-- lookup parent from repository -->
    </parent>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.keevol.springboot</groupId>
            <artifactId>currency-rates-service</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project> 
```

现在，我们要向这个 pom.xml 中添加“佐料”，添加的原则就是前面所说的那样，一个是 Scala 编译期的依赖，一个是 Scala 运行期的依赖，添加完“佐料”的 pom.xml 如下：

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.keevol.springboot.chapter5</groupId>
    <artifactId>currency-webapi-with-scala</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>currency-webapi-with-scala</name>
    <description>Demo project for Spring Boot</description>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.1.RELEASE</version>
        <relativePath /> <!-- lookup parent from repository -->
    </parent>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
        <scala.version>2.11.7</scala.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>${scala.version}</version>
        </dependency>
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-compiler</artifactId>
            <version>${scala.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.keevol.springboot</groupId>
            <artifactId>currency-rates-service</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>3.2.2</version>
                <executions>
                    <execution>
                        <id>compile-scala</id>
                        <phase>compile</phase>
                        <goals>
                            <goal>add-source</goal>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>test-compile-scala</id>
                        <phase>test-compile</phase>
                        <goals>
                            <goal>add-source</goal>
                            <goal>testCompile</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <recompileMode>incremental</recompileMode>
                    <scalaVersion>${scala.version}</scalaVersion>
                    <args>
                        <arg>-deprecation</arg>
                    </args>
                    <jvmArgs>
                        <jvmArg>-Xms64m</jvmArg>
                        <jvmArg>-Xmx1024m</jvmArg>
                    </jvmArgs>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

其中，scala-maven-plugin 的各项自定义配置只是演示，大家可以根据实际情况进行相应配置项的调整，比如针对 Scala 编译器的输入参数，或者 jvmArgs 的配置。

按照约定，scala 代码一般都放在 src/main/scala 源代码目录下，既然 Maven 的构建描述符已准备好，下面我们就准备开始写 Scala 代码。

下面是 Web API 对应的 Controller 实现：

```

// ScalaCurrencyRateQueryController.scala 源码文件
@RestController
class ScalaCurrencyRateQueryController {
    @Autowired var currencyRateService:CurrencyRateService=_
    @RequestMapping(value=Array("/"),method=Array(RequestMethod.GET))
    def quote(symbol: String): WebApiResponse[ExchangeRate] = {
        val response: WebApiResponse[ExchangeRate] = new WebApiResponse [ExchangeRate]
        response.setCode(WebApiResponse.SUCCESS_CODE)
        response.setData(currencyRateService.quote(CurrencyPair.from(symbol)))
        response
    }
}
```

不同于 Java 将实例代码和静态代码都纳入同一个结构体，Scala 将这两种结构剥离为各自独立的实体，所以，main 启动类现在放在了 ScalaCurrency-WebApiApplication 的 companion object 中。

现在，只要通过 mvn package，然后 java-jar currency-webapi-with-scala-0.0.1-SNAPSHOT.jar 就可以顺利启动这个 Scala 开发的 SpringBoot 微服务了。

使用 Maven 构建 Scala 的 SpringBoot 微服务项目不单单只是开发期间过渡很平滑，其最大的好处更在于，原来围绕 Maven 打造的 SpringBoot 微框架的各项支持依然有效，比如 spring-boot-maven-plugin。

而且，开发完的基于 Scala 的 SpringBoot 微服务可以无缝地衔接到我们自己的微服务交付链路中去（之前围绕着 Java 和 SpringBoot 打造的交付链路基础设施持续有效）。

一旦通过微服务的发布和部署平台交付完成，基于 Scala 的 SpringBoot 微服务可以享受基于 Java 的 SpringBoot 微服务同样的待遇，从服务注册和发现，到监控与运维，甚至安全防护。

由于篇幅有限，请点击下面链接进行阅读：

*   简化基于 Maven 的 Scala 项目创建详解
*   简化基于 Scala 的 Web API 开发