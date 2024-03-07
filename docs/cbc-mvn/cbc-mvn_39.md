# 通过 Maven 过滤 Web 资源

> 原文：[`c.biancheng.net/view/5288.html`](http://c.biancheng.net/view/5288.html)

在 Web 项目中，src/main/resources 目录下的资源文件会打包到 war 的 WEB-INF/classes 目录下，也是 Java 代码编译后的 class 所在的目录。

换句话说，这些资源文件打包后，会放在应用程序的 classpath 目录中。另外，还有 src/main/webapp 目录下的资源文件，打包后会放在 war 的根目录下。

同一般的资源文件一样，Web 资源文件默认是不会被过滤的，即使开启一般资源文件的过滤功能，也不会影响 Web 资源文件。

不过有的用户希望在构建项目的时候为不同客户使用不一样的资源文件。比如，需要为不同客户构建不同的 logo 图片。这时候，就可以在 Web 资源文件中使用 Maven 属性来达到灵活构建的效果。

例如，可以用＄{client.logo} 表示客户的图片名称，然后在 profile 中做如下定义。

```

<profiles>
    <profile>
        <id>client-cyedu</id>
        <properties>
            <client.logo>cyedu.jpg</client.logo>
        </properties>
    </profile>
    <profile>
        <id>client-maven</id>
        <properties>
            <client.logo>maven.jpg</client.logo>
        </properties>
    </profile>
</profiles>
```

接下来在 pom 中配置 maven-war-plugin 插件，对 src/main/webapp 资源目录开启过滤功能，代码如下：

```

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>2.6</version>
    <configuration>
        <webResources>
            <resource>
                <filtering>true</filtering>
                <directory>src/main/webapp</directory>
                <includes>
                    <include>**/*.css</include>
                    <include>**/*.js</include>
                </includes>
            </resource>
        </webResources>
    </configuration>
</plugin>
```

如上代码所示，使用 directory 指定要开启过滤功能的目录为 src/main/webapp，使用 includes 元素指定要过滤的文件，这里是所有的 CSS 和 JS 文件。

完成上面的配置，使用命令“mvn clean install-Pclient-cyedu”表示使用 cyedu.jpg 图片，使用命令“mvn clean install-Pclient-maven”表示使用 maven.jpg 图片。