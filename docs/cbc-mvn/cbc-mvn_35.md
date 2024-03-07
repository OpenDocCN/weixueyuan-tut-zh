# Maven 测试代码的重用

> 原文：[`c.biancheng.net/view/5283.html`](http://c.biancheng.net/view/5283.html)

在项目开发过程中，程序员经常要将公共的功能代码打包共享给其他模块重复使用，此外还有以前沉淀下来的公共框架代码。这里可以使用 mvn package 命令将这些代码打包，构建成构件，发布到仓库中共享，以便其他项目可以做依赖构件使用。

同样地，对那些有着良好设计，能够重复使用在项目的不同模块中、甚至不同项目中的测试代码，也需要打包成构件重复使用，从而减少编写测试代码的工作量。而 mvn package 只会对主代码和资源文件进行打包安装与部署，对测试代码和资源文件是不会处理的。

为了实现将测试代码和资源文件打包安装与部署，可以在 pom 中配置 maven-jar-plugin 插件，样例配置代码如下：

```

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>2.2</version>
    <executions>
        <execution>
            <goals>
                <goal>test-jar</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

maven-jar-plugin 有两个目标：一个是 jar；另一个是 test-jar。jar 目标有内置绑定到 Maven 的 default 生命周期的 package 阶段，会在 Maven 工程进行构建的时候自动执行，将项目的主代码和资源文件进行打包。

test-jar 目标没有内置绑定，所以需要用户在插件配置中声明该目标，从而达到在 Maven 工程构建的时候将测试代码和资源文件打包。test-jar 目标是默认绑定到 default 生命周期的 package 阶段，所以当运行 mvn clean package 命令的时候，能同时将主代码和测试代码分别打包。

最后需要说明的是，当使用测试构件依赖的时候，需要指定依赖的 type 为 test-jar，样例配置代码如下：

```

<dependency>
    <groupId>...</groupId>
    <artifactId>...</artifactId>
    <version>...</version>
    <type>test-jar</type>
    <score>test</score>
</dependency>
```