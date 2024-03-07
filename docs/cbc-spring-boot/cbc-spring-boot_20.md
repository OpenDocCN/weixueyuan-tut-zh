# 创建基于 Dubbo 框架的 SpringBoot 微服务

> 原文：[`c.biancheng.net/view/4668.html`](http://c.biancheng.net/view/4668.html)

Dubbo 是原阿里巴巴 B2B 平台技术部倾力打造的一款开源且优秀的面向 Java 平台的服务框架，历经三次大的迭代，从最早盲从 OSGi 的坑里跳出来，再到序列化协议和通信框架的定型，最终才完善和成型，可以说，国内开源服务化框架中无出其右者。

但是，Dubbo 研发的年代处于 Spring 框架的 XSD 时代，所以，使用上还是会更多以 XML 形式定义服务和访问服务，但好在 SpringBoot 框架在 IoC 容器的配置方式上“不挑食”，所以，我们还是可以让 Dubbo 和 SpringBoot 框架友好相处。

直接使用 Dubbo 开发微服务了，为什么还要用 SpringBoot？，这是一个很好的问题。

使用 SpringBoot 开发基于 Dubbo 框架的微服务的原因就是，我们将 Dubbo 微服务以 SpringBoot 形式标准化了。如此一来，我们既可以享受 SpringBoot 框架和周边的一系列研发支持，还可以用统一的形式发布、部署和运维。

微服务的一个特点就是数量多，一个人应对 1000 个相同操作接口的微服务和一个人应对 1000 个不同操作接口的微服务相比来说，相信你会选择前者。

2015 年到 2016 年，美元升值预期持续升温，大家想必对汇率也比较关注，所以，我们不妨实现一个基于央行汇率的查询服务。

#### 1\. 定义汇率查询服务接口

我们新建 Maven 工程 currency-rates-api：

```

<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.keevol.springboot</groupId>
    <artifactId>currency-rates-api</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>currency-rates-api</name>
    <url>http://maven.apache.org</url>
    <properties>
        <java_source_version>1.8</java_source_version>
        <java_target_version>1.8</java_target_version>
        <file_encoding>UTF-8</file_encoding>
    </properties>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>${java_source_version}</source>
                    <target>${java_target_version}</target>
                    <encoding>${file_encoding}</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-javadoc-plugin</artifactId>
                <configuration>
                    <charset>${file_encoding}</charset>
                    <encoding>${file_encoding}</encoding>
                    <additionalparam>-Xdoclint:none</additionalparam>
                </configuration>
                <executions>
                    <execution>
                        <id>attach-javadocs</id>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project> 
```

然后定义汇率查询服务接口：

```

public interface CurrencyRateService {
    ExchangeRate quote(CurrencyPair currencyPair) throws IOException;
} 
```

之后通过 mvn install 或者 mvn deploy 将 currency-rates-api 发布到本地或者远程 maven 仓库。

#### 2\. 实现汇率查询服务

新建 Maven 工程 currency-rates-service：

```

<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.keevol.springboot</groupId>
    <artifactId>currency-rates-service</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>currency-rates-service</name>
    <url>http://maven.apache.org</url>
    <properties>
        <java_source_version>1.8</java_source_version>
        <java_target_version>1.8</java_target_version>
        <file_encoding>UTF-8</file_encoding>
        <spring_version>4.1.6.RELEASE</spring_version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>com.keevol.springboot</groupId>
            <artifactId>currency-rates-api</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
            <version>2.5.3</version>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring_version}</version>
            <exclusions>
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring_version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring_version}</version>
        </dependency>
    </dependencies>
</project> 
```

主要关注针对 currency-rates-api 以及 Dubbo 框架的依赖定义。

项目创建完成后，我们着手实现服务接口：

```

public class CurrencyRateServiceImpl implements CurrencyRateService {
    private CurrencyRateRepository rateRepository;

    public ExchangeRate quote(CurrencyPair currencyPair) throws IOException {
        return rateRepository.get(currencyPair);
    }

    public CurrencyRateRepository getRateRepository() {
        return rateRepository;
    }

    public void setRateRepository(CurrencyRateRepository rateRepository) {
        this.rateRepository = rateRepository;
    }
}
```

其中，CurrencyRateRepository 可以根据情况给出相应的实现，比如通过直接对接银行的系统获取汇率或者通过爬取官网数据获得数据都可以，这里不做过多解释。

服务实现之后，我们需要通过 Dubbo 框架暴露出去给外部使用，所以，我们通过 Dubbo 的服务描述文件（即标准的 Spring 框架 XML 形式的配置文件）完成最终服务的对外开放：

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:util="http://www.springframework.org/schema/util"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:c="http://www.springframework.org/schema/c"
    xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/util
        http://www.springframework.org/schema/util/spring-util.xsd
        http://code.alibabatech.com/schema/dubbo
        http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    <dubbo:application name="${dubbo.application.name}"
        owner="${dubbo.application.owner}" />
    <dubbo:protocol
        name="${dubbo.application.protocol.name}"
        port="${dubbo.application.protocol.port}" />
    <dubbo:service
        interface="com.keevol.springboot.services.currency.rates.CurrencyRateService"
        ref="serviceImpl" registry="N/A" />
    <bean id="serviceImpl"
        class="com.keevol.springboot.services.currency.rates.CurrencyRateServiceImpl" />
</beans>
```

因为只是原型示例，所以，我们没有定义更加严谨的服务注册方式（registry="N/A"），生产环境下，大家还是需要选择合适的注册服务，比如 Zookeeper。

使用 Dubbo 框架的服务不依赖传统 J2EE 的容器对外提供服务，而是以独立进程的形式对外服务，所以，我们还需要提供一个 Bootstrap 类用于启动我们的 Dubbo 服务：

```

public class Bootstrap {
    public static void main(String[] args) throws IOException {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring/services.xml");
        ((AbstractApplicationContext) context).registerShutdownHook();
        System.in.read();
    }
}
```

至此，一个独立部署运行的 Dubbo 服务宣告完成。

#### 3\. 没有 SpringBoot 什么事儿

不管是否使用 SpringBoot，基于 Dubbo 框架的服务从其服务的定义，到服务的实现，都是常规而无法省略的工作，但是：

*   服务完成后，启动 main 函数里的逻辑貌似每次都要编写一样的？
*   服务完成后，以什么样的形式发布并部署？zip 包，还是 jar 包，亦或 war 包，甚至其他形式？

考虑到这些，就会涉及 SpringBoot，一旦将 Dubbo 服务以 SpringBoot 的形式封装，Dubbo 服务就可以既享受 SpringBoot 的开发便捷性，又能以统一的形式发布和部署（比如可执行的 jar 形式）。

虽然我们无法省略和简化服务的定义和实现这些步骤，但 Dubbo 服务的 main 函数逻辑实际上是可以固化下来并且复用的，否则，每个人给出的 Dubbo 服务实现的启动类都可能不一样，进而导致运维操作不一样。

在上面我们给出的 main 函数实现中，为了阻止 Dubbo 服务的进程退出（主线程执行完毕，无其他非 daemon 线程存活）。

我们使用了 IO 操作来达成这一目的（System.in.read（）），但实际上，这不是一个很好的做法。更好的做法是，我们设置一个服务是否关闭的开关，只有当外部调用相应管理接口将服务关闭之后，再关闭当前的 Dubbo 服务，所以，基于此思路，我们可以实现一个 ShutdownLatch：

```

public interface ShutdownLatchMBean {
    String shutdown();
}

public class ShutdownLatch implements ShutdownLatchMBean {
    protected AtomicBoolean running = new AtomicBoolean(false);
    public long checkIntervalInSeconds = 10;
    private String domain = "com.wacai.lifecycles";

    public ShutdownLatch() {
    }

    public ShutdownLatch(String domain) {
        this.domain = domain;
    }

    public void await() throws Exception {
        if (running.compareAndSet(false, true)) {
            MBeanServer mBeanServer = ManagementFactory.get - PlatformMBeanServer();
            mBeanServer.registerMBean(this, new ObjectName(domain, "name", "ShutdownLatch"));
            while (running.get()) {
                TimeUnit.SECONDS.sleep(checkIntervalInSeconds);
            }
        }
    }

    @Override
    public String shutdown() {
        if (running.compareAndSet(true, false)) {
            return "shutdown signal sent, shutting down..";
        } else {
            return "shutdown signal had been sent, no need again and again and again...";
        }
    }

    public static void main(String[] args) throws Exception {
        ShutdownLatch latch = new ShutdownLatch("your_domain_for_mbeans");
        latch.await();
    }
}
```

ShutdownLatch 默认以 JMX 的 MBean 暴露为管理接口，所以，Dubbo 服务的 main 函数可以规范为：

```

ApplicationContext context = new ClassPathXmlApplicationContext("spring/services.xml");
((AbstractApplicationContext) context).registerShutdownHook();
ShutdownLatch latch = new ShutdownLatch("your_domain_for_mbeans");
latch.await();
```

为了简化基于 SpringBoot 的 Dubbo 服务开发，我们可以将针对 Dubbo 框架的依赖以及对服务启动类的规范封装为一个 spring-boot-starter-dubbo 这样的自动配置模块，此后，要开发一个基于 SpringBoot 的 Dubbo 服务，只要依赖这一自动配置模块即可。

所以，我们新建 Maven 项目 spring-boot-starter-dubbo：

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.keevol.springboot</groupId>
    <artifactId>spring-boot-starter-dubbo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>spring-boot-starter-dubbo</name>
    <description>Demo project for Spring Boot</description>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.1.RELEASE</version>
        <relativePath /> <!-- lookup parent from repository -->
    </parent>
    <properties>
        <java_source_version>1.8</java_source_version>
        <java_target_version>1.8</java_target_version>
        <file_encoding>UTF-8</file_encoding>
        <spring_version>4.1.6.RELEASE</spring_version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
            <version>2.5.3</version>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring</artifactId>
                </exclusion>
            </exclusions>
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

项目的 pom.xml 中，我们可以重点关注针对 spring-boot-starter 和 Dubbo 框架的依赖。

所有的 SpringBoot 应用启动都是基于标准的 SpringApplication.run 完成的，为了在启动类执行完成后可以阻止 Dubbo 服务进程的推出，我们需要在 SpringBoot 启动类的 main 函数最后调用 ShutdownLatch.await（）。

但是如果要求每个开发者在自己的 SpringBoot 启动类中调用这段代码，显然这并没有给开发者的工作带来任何简化，所以，我们选择使用 CommandLineRunner 来完成针对 ShutdownLatch.await() 的调用工作。

教程前面已经介绍过 CommandLineRunner，任何注册到 SpringBoot 应用的 CommandLineRunner 都将在 SpringApplication.run 执行完成后再执行，恰好符合我们当前场景需要，代码如下所示：

```

public class DubboServiceLatchCommandLineRunner implements CommandLineRunner {
    private String domain = "com.keevol.services.management";

    @Override
    public void run(String... args) throws Exception {
        ShutdownLatch latch = new ShutdownLatch(getDomain());
        latch.await();
    }

    public String getDomain() {
        return domain;
    }

    public void setDomain(String domain) {
        this.domain = domain;
    }
}
```

然后我们需要将 DubboServiceLatchCommandLineRunner 注册到 SpringBoot 应用的容器之中，所以，定义一个 JavaConfig 类如下：

```

@Configuration
@Order
public class DubboAutoConfiguration {
    protected Logger logger = LoggerFactory.getLogger(DubboAutoConf - iguration.class);
    @Value("${shutdown.latch.domain.name: com.keevol.services.management}")
    private String shutdownLatchDomainName;
    @Bean
    @ConditionalOnClass(name = "com.alibaba.dubbo.rpc.Exporter")
    public DubboServiceLatchCommandLineRunner configureDubboService-LatchCommandLineRunner() {
        logger.debug("DubboAutoConfiguration enabled by adding Dubbo-ServiceLatchCommandLineRunner.");
        DubboServiceLatchCommandLineRunner runner = new DubboServi-ceLatchCommandLineRunner();
        runner.setDomain(shutdownLatchDomainName);
        return runner;
    }
}
```

我们使用 @Order 标注了该配置类以保证 DubboServiceLatchCommandLineRunner 在最后执行，以避免阻塞其他逻辑的执行。

万里长征走到了最后一步，要实现一个 SpringBoot 的自动配置模块，我们需要将 DubboAutoConfiguration 配置类通过 META-INF/spring.factories 配置文件注册并发布：

org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.keevol.springboot.dubbo.autoconfigure.DubboAutoConfiguration 

至此，我们只要通过 mvn install 或者 mvn deploy 将 spring-boot-starter-dubbo 发布到本地或者远程 Maven 仓库，以后开发 Dubbo 服务就只需要在服务的项目依赖中添加如下依赖：

```

<dependency>
    <groupId>com.keevol.springboot</groupId>
    <artifactId>spring-boot-starter-dubbo</artifactId>
    <version>1.0.0</version>
</dependency>
```

然后以标准的 SpringApplication 加载 Dubbo 服务的配置并启动就可以了：

```

@SpringBootApplication
public class DubboWithSpringbootApplication {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(Dubb - oWithSpringbootApplication.class,
                "classpath*:/spring/**/*.xml");
        application.run(args);
    }
}
```

当然，规范 Dubbo 依赖以及服务的启动逻辑只是使用 SpringBoot 获得的好处之一，最主要的，我们提供了一种基于 SpringBoot 的标准化的 Dubbo 服务开发和发布实践，这对于海量微服务的开发和运维来说是很重要的。