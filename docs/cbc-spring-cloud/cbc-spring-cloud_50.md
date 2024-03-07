# Apollo 在 Java 中的使用

> 原文：[`c.biancheng.net/view/5474.html`](http://c.biancheng.net/view/5474.html)

本节主要讲解在普通的 Java 项目和 Spring Boot 中如何使用 Apollo。

## 普通 Java 项目中使用

加入 Apollo Client 的 Maven 依赖，代码如下所示。

<dependency>
    <groupId>com.ctrip.framework.apollo</groupId>
    <artifactId>apollo-client</artifactId>
    <version>1.1.0</version>
</dependency>

使用 API 的方式来获取配置，具体代码如下所示。

```

public class App {
    public static void main(String[] args) {
        Config config = ConfigService.getAppConfig();
        String key = "username";
        String defaultValue = "张三";
        String username = config.getProperty(key, defaultValue);
        System.out.println("username=" + username);
    }
}
```

通过 ConfigService 得到 Config 对象，config.getProperty() 方法可以传入你想获取的配置 Key，defaultValue 是当配置中心找不到配置的时候返回的默认值，避免空指针异常。

运行上面这段代码，输出的结果是默认值“张三”。因为我们还没有指定 Apollo 需要的一些必要信息，这些信息包括 Meta Server、AppId 和 Environment。Cluster 可以不用指定，用默认即可。

#### 1\. Meta Server 配置

Apollo 支持应用在不同的环境中有不同的配置，所以需要运行提供给 Apollo 客户端当前环境的 Apollo Meta Server 信息。

在默认情况下，meta server 和 config service 是部署在同一个 JVM 进程里的，所以 meta server 的地址就是 config service 的地址。

目前我们用的快速启动包只有一个 DEV 环境，config service 的地址是 http://localhost：8080，这个已经在启动脚本 demo.sh 中定义好了。

为了能够让示例代码在各位读者的电脑上也能直接运行，我们将配置定在 classpath：/META-INF/app.properties 中。内容为 apollo.meta=http://localhost：8080。

#### 2\. APPid 配置

APPid 是应用的身份信息，是从服务端获取配置的一个重要信息。同样 APPid 的配置方式也有多种，我们采用跟 Meta Server 一样的方式，配置在 classpath：/META-INF/app.properties 中。内容为 app.id=SampleApp。

SampleApp 在 Portal 的项目主页面中有展示，如果是你自己新建的项目，那么就是你自定义的 AppId。

#### 3\. Environment 配置

Environment 跟项目本身没有关系，一个项目可以部署在不同的环境中，代码不需要改变，需要变化的只是配置值而已。所以 Environment 的配置不能配置在项目中，最常用的有如下两种配置方式。

#### 1）通过 Java System Property

*   可以通过 Java 的 System Property env 来指定环境。
*   在 Java 程序启动脚本中，可以指定 -Denv=YOUR-ENVIRONMENT。
*   如果是运行 jar 文件，需要注意格式为 java-Denv=YOUR-ENVIRONMENT-jar xxx.jar。
*   注意 key 为全小写。

#### 2）通过配置文件

*   最后一个推荐的方式是通过配置文件来指定 env=YOUR-ENVIRONMENT。
*   对于 Mac/Linux，文件位置为 /opt/settings/server.properties。
*   对于 Windows，文件位置为 C：\opt\settings\server.properties。

server.properties 内容为 env=DEV。

同样的，为了能够让示例代码能够更方便地在各位读者的电脑上运行，我们就用 ava System Property 的方式来指定 Environment，要么在 IDE 的启动参数中指定，要么就在 main 方法的第一行通过代码指定（仅供开发演示用，不能用于生产环境）。具体代码所示。

public static void main(String[] args) {
    System.setProperty("env", "DEV");
    // ....
}

所有配置完成之后，我们再次运行前面的示例代码，可以看到输出的内容就是我们自己配置的值。

#### 4\. 监听配置变化事件

在某些场景下，当配置发生变化的时候，我们需要进行一些特殊的处理。比如，数据库连接串变化后需要重建连接等，就可以使用 API 提供的监听机制。具体代码如下所示。

```

config.addChangeListener(new ConfigChangeListener() {
    public void onChange(ConfigChangeEvent changeEvent) {
        System.out.println("发生修改数据的命名空间是：" + changeEvent.getNamespace());
        for (String key : changeEvent.changedKeys()) {
            ConfigChange change = changeEvent.getChange(key);
            System.out.println(
                    String.format("发现修改 - 配置 key: %s, 原来的值: %s, 修改后的值: %s, 操作类型: %s", 
                            change.getPropertyName(),
                            change.getOldValue(), change.getNewValue(), change.getChangeType()));
        }
    }
});
```

当我们在 Portal 中进行修改配置时，就会触发监听事件，输出结果为：

发生修改数据的命名空间是：application
发现修改 - 配置 key: username, 原来的值: zhangsan, 修改后的值: zhangsan1, 操作类型: MODIFIED

## Spring Boot 中使用

首先准备一个 Spring Boot 项目，加入 Apollo Client 的 Maven 依赖，具体代码如下所示：

<dependency>
    <groupId>com.ctrip.framework.apollo</groupId>
    <artifactId>apollo-client</artifactId>
    <version>1.1.0</version>
</dependency>

然后配置 Apollo 的信息，配置放在 application.properties 中：

app.id=SampleApp
apollo.meta=http://localhost:8080
apollo.bootstrap.enabled=true
apollo.bootstrap.namespaces=application

其中，

*   app.id：身份信息。
*   apollo.meta：Meta Server（Config Service）。
*   apollo.bootstrap.enabled：项目启动的 bootstrap 阶段，向 Spring 容器注入配置信息。
*   apollo.bootstrap.namespaces：注入命名空间。

环境同样在 main 方法中指定，代码如下所示。

```

@SpringBootApplication
public class App {
    public static void main(String[] args) {
        // 指定环境(仅供开发演示用, 不能用于生产环境))
        System.setProperty("env", "DEV");
        SpringApplication.run(App.class, args);
    }
}
```

#### 1\. Placeholder 注入配置

Placeholder 注入配置代码如下所示。

/**
* 用户名, 默认值为 zhangsan
*/
@Value("${username:zhangsan}")
private String username;

#### 2\. Java Config 使用方式

Java Config 使用方式具体代码如下所示。

```

@Data
@Configuration
public class UserConfig {
    @Value("${username:zhangsan}")
    private String username;
}
```

使用 Config 配置类注入具体代码如下所示：

@Autowired
private UserConfig userConfig;

#### 3\. ConfigurationProperties 使用方式

ConfigurationProperties 的使用方法具体代码如下所示。

```

@Data
@Configuration
@ConfigurationProperties(prefix = "redis.cache")
public class RedisConfig {
    private String host;
}
```

配置中心只需要增加 redis.cache.host 配置项即可实现注入，配置内容如下：

redis.cache.host = 192.168.1.1

ConfigurationProperties 方式有个缺点，当配置的值发生变化时不会自动刷新，而是需要手动实现刷新逻辑，笔者建议大家不要使用这种方式，比较繁琐。

如果有配置需要加统一前缀的方式可以用 Java Config 的方式代替。

#### 4\. Spring Annotation 支持

#### 1）@ApolloConfig

用来自动注入 Apollo Config 对象，代码如下所示。

```

@ApolloConfig
private Config config;

@GetMapping("/config/getUserName3")
public String getUserName3() {
    return config.getProperty("username", "zhangsan");
}
```

#### 2）@ApolloConfigChangeListener

用来自动注册 ConfigChangeListener，代码如下所示。

```

@ApolloConfigChangeListener
private void someOnChange(ConfigChangeEvent changeEvent) {
    if(changeEvent.isChanged("username")) {
        System.out.println("username 发生修改了");
    }
}
```

#### 3）@ApolloJsonValue

用来把配置的 JSON 字符串自动注入为对象。

定义一个实体类，代码如下所示。

@Data
public class Student {
    private int id;
    private String name;
}

对象注入，代码如下所示。

@ApolloJsonValue("${stus:[]}")
private List<Student> stus;

后台增加配置内容如下：

stus = [{"id":1,"name":"jason"}]