# spring-boot-starter-web 和快速 Web 应用开发

> 原文：[`c.biancheng.net/view/4641.html`](http://c.biancheng.net/view/4641.html)

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

## 项目结构层面的约定

项目结构层面与传统打包为 war 的 Java Web 应用的差异在于，静态文件和页面模板的存放位置变了，原来是放在 src/main/webapp 目录下的一系列资源，现在都统一放在 src/main/resources 相应子目录下，比如：

*   src/main/resources/static 用于存放各类静态资源，比如 css，js 等。
*   src/main/resources/templates 用于存放模板文件，比如 *.vm。

当然，如果还是希望以 war 包的形式，而不是 SpringBoot 推荐使用的独立 jar 包形式发布 Web 应用，也可以继续原来 Java Web 应用的项目结构约定。

## SpringMVC 框架层面的约定和定制

spring-boot-starter-web 默认将为我们自动配置如下一些 SpringMVC 必要组件：

*   必要的 ViewResolver，比如 ContentNegotiatingViewResolver 和 Bean-NameViewResolver。
*   将必要的 Converter、GenericConverter 和 Formatter 等 bean 注册到 IoC 容器。
*   添加一系列的 HttpMessageConverter 以便支持对 Web 请求和相应的类型转换。
*   自动配置和注册 MessageCodesResolver。
*   其他。

任何时候，如果我们对默认提供的 SpringMVC 组件设定不满意，都可以在 IoC 容器中注册新的同类型的 bean 定义来替换，或者直接提供一个基于 WebMvcConfigurerAdapter 类型的 bean 定义来定制，甚至直接提供一个标注了 @EnableWebMvc 的 @Configuration 配置类完全接管所有 SpringMVC 的相关配置，自己完全重新配置。