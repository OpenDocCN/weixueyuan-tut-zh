# Eureka 注册中心开启密码认证

> 原文：[`c.biancheng.net/view/5325.html`](http://c.biancheng.net/view/5325.html)

Eureka 自带了一个 Web 的管理页面，方便我们查询注册到上面的实例信息，但是有一个问题：如果在实际使用中，注册中心地址有公网 IP 的话，必然能直接访问到，这样是不安全的。所以我们需要对 Eureka 进行改造，加上权限认证来保证安全性。

改造我们的 eureka-server，通过集成 Spring-Security 来进行安全认证。

在 pom.xml 中添加 Spring-Security 的依赖包，代码如下所示。

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

然后在 application.properties 中加上认证的配置信息：

spring.security.user.name=yinjihuan #用户名
spring.security.user.password=123456 #密码

增加 Security 配置类：

```

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 关闭 csrf
        http.csrf().disable();
        // 支持 httpBasic
        http.authorizeRequests().anyRequest().authenticated().and().httpBasic();
    }
}
```

重新启动注册中心，访问 http://localhost:8761/，此时浏览器会提示你输入用户名和密码，输入正确后才能继续访问 Eureka 提供的管理页面。

在 Eureka 开启认证后，客户端注册的配置也要加上认证的用户名和密码信息：

eureka.client.serviceUrl.defaultZone=http://zhangsan:123456@localhost:8761/eureka/