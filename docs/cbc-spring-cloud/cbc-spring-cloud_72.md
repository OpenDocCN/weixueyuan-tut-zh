# 使用 Zuul 聚合多个微服务的 Swagger 文档

> 原文：[`c.biancheng.net/view/5535.html`](http://c.biancheng.net/view/5535.html)

在 Zuul 中进行聚合操作的原因是不想每次都去访问独立服务的文档，通过网关统一整合这些服务的文档方便使用。

在网关中加入 Swagger 的 Maven 依赖，代码如下所示。

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

自定义配置进行整合，笔者采用了一种比较简单的方式，不是手动的去配置要整合的服务信息，而是直接去读取 Eureka 中的服务信息，只要是 Eureka 中的服务就都能整合进来，代码如下所示。

```

@EnableSwagger2
@Component
@Primary
public class DocumentationConfig implements SwaggerResourcesProvider {

    @Autowired
    private DiscoveryClient discoveryClient;

    @Value("${spring.application.name}")
    private String applicationName;

    @Override
    public List<SwaggerResource> get() {
        List<SwaggerResource> resources = new ArrayList<>();
        // 排除自身, 将其他的服务添加进去
        discoveryClient.getServices().stream().filter(s -> !s.equals(applicationName)).forEach(name -> {
            resources.add(swaggerResource(name, "/" + name + "/v2/api-docs", "2.0"));
        });
        return resources;
    }

    private SwaggerResource swaggerResource(String name, String location, String version) {
        SwaggerResource swaggerResource = new SwaggerResource();
        swaggerResource.setName(name);
        swaggerResource.setLocation(location);
        swaggerResource.setSwaggerVersion(version);
        return swaggerResource;
    }
}
```