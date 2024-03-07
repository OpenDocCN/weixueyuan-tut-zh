# spring-boot-starter-logging 和 spring-boot-starter-web

> 原文：[`c.biancheng.net/view/4639.html`](http://c.biancheng.net/view/4639.html)

本节主要讲解 spring-boot-starter-logging 和 spring-boot-starter-web 两个常见通用的 spring-boot-starter 模块。

## 应用日志和 spring-boot-starter-logging

Java 的日志系统多种多样，从 java.util 默认提供的日志支持，到 log4j，log4j2，commons logging 等，复杂繁多，所以，应用日志系统的配置就会比较特殊，从而 spring-boot-starter-logging 也比较特殊一些，下面将其作为我们第一个了解的自动配置依赖模块。

假如 maven 依赖中添加了 spring-boot-starter-logging，如以下代码所示：

```

<dependency>
    <groupId> org.springframework.boot </groupId>
    <artifactId> spring-boot-starter-logging </artifactId>
</dependency>
```

那么，我们的 SpringBoot 应用将自动使用 logback 作为应用日志框架，SpringBoot 启动的时候，由 org.springframework.boot.logging.Logging-Application-Listener 根据情况初始化并使用。

SpringBoot 为我们提供了很多默认的日志配置，所以，只要将 spring-boot-starter-logging 作为依赖加入到当前应用的 classpath，则“开箱即用”，不需要做任何多余的配置，但假设我们要对默认 SpringBoot 提供的应用日志设定做调整，则可以通过几种方式进行配置调整：

*   遵循 logback 的约定，在 classpath 中使用自己定制的 logback.xml 配置文件。
*   在文件系统中任何一个位置提供自己的 logback.xml 配置文件，然后通过 logging.config 配置项指向这个配置文件来启用它，比如在 application.properties 中指定如下的配置。

logging.config=/{some.path.you.defined}/any-logfile-name-I-like.log

SpringBoot 默认允许我们通过在配置文件或者命令行等方式使用 logging.file 和 logging.path 来自定义日志文件的名称和存放路径，不过，这只是允许我们在 SpringBoot 框架预先定义的默认日志系统设定的基础上做有限的设置，如果我们希望更灵活的配置，最好通过框架特定的配置方式提供相应的配置文件，然后通过 logging.config 来启用。

如果大家更习惯使用 log4j 或者 log4j2，那么也可以采用类似的方式将它们对应的 spring-boot-starter 依赖模块加到 Maven 依赖中即可：

```

<dependency>
    <groupId> org.springframework.boot </groupId>
    <artifactId> spring-boot-starter-log4j </artifactId>
</dependency>
```

或者

```

<dependency>
    <groupId> org.springframework.boot </groupId>
    <artifactId> spring-boot-starter-log4j2 </artifactId>
</dependency>
```

但一定不要将这些完成同一目的的 spring-boot-starter 都加到依赖中。

## 快速 Web 应用开发与 spring-boot-starter-web

在这个互联网时代，使用 Spring 框架除了开发少数的独立应用，大部分情况下实际上在使用 SpringMVC 开发 web 应用，为了帮我们简化快速搭建并开发一个 Web 项目，SpringBoot 为我们提供了 spring-boot-starter-web 自动配置模块。

只要将 spring-boot-starter-web 加入项目的 maven 依赖：

```

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

我们就得到了一个直接可执行的 Web 应用，当前项目下运行 mvn spring-boot：run 就可以直接启动一个使用了嵌入式 tomcat 服务请求的 Web 应用，只不过，我们还没有提供任何服务 Web 请求的 Controller，所以，访问任何路径都会返回一个 SpringBoot 默认提供的错误页面（一般称其为 whitelabel error page），我们可以在当前项目下新建一个服务根路径 Web 请求的 Controller 实现：

```

@RestController
public class IndexController {
    @RequestMapping("/")
    public String index() {
        return "hello, there";
    }
}
```

重新运行 mvn spring-boot：run 并访问 http://localhost：8080，错误页面将被我们的 Controller 返回的消息所替代，一个简单的 Web 应用就这样完成了。

但是，简单的背后，其实却有很多“潜规则”（约定），我们只有充分了解了这些“潜规则”，才能更好地应用 spring-boot-starter-web。

#### 项目结构层面的约定

项目结构层面与传统打包为 war 的 Java Web 应用的差异在于，静态文件和页面模板的存放位置变了，原来是放在 src/main/webapp 目录下的一系列资源，现在都统一放在 src/main/resources 相应子目录下，比如：

*   src/main/resources/static 用于存放各类静态资源，比如 css，js 等。
*   src/main/resources/templates 用于存放模板文件，比如 *.vm。

当然，如果还是希望以 war 包的形式，而不是 SpringBoot 推荐使用的独立 jar 包形式发布 Web 应用，也可以继续原来 Java Web 应用的项目结构约定。

#### SpringMVC 框架层面的约定和定制

spring-boot-starter-web 默认将为我们自动配置如下一些 SpringMVC 必要组件：

*   必要的 ViewResolver，比如 ContentNegotiatingViewResolver 和 Bean-NameViewResolver。
*   将必要的 Converter、GenericConverter 和 Formatter 等 bean 注册到 IoC 容器。
*   添加一系列的 HttpMessageConverter 以便支持对 Web 请求和相应的类型转换。
*   自动配置和注册 MessageCodesResolver。
*   其他。

任何时候，如果我们对默认提供的 SpringMVC 组件设定不满意，都可以在 IoC 容器中注册新的同类型的 bean 定义来替换，或者直接提供一个基于 WebMvcConfigurerAdapter 类型的 bean 定义来定制，甚至直接提供一个标注了 @EnableWebMvc 的 @Configuration 配置类完全接管所有 SpringMVC 的相关配置，自己完全重新配置。