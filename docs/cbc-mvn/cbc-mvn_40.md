# 在 Maven profile 中激活集成测试

> 原文：[`c.biancheng.net/view/5290.html`](http://c.biancheng.net/view/5290.html)

在正规项目开发过程中，开发人员和测试人员需要编写大量的测试案例对项目代码测试。这些测试案例一般分为单元测试和集成测试。

单元测试是对单个类中的一个个方法进行测试，比较具体，运行速度快。集成测试属于模块级测试，比较宏观，涉及的内容多，运行速度慢。

在项目后期的集成和系统测试阶段，频繁地执行集成测试案例是有必要的，但是在平常开发过程中还自动执行集成测试案例的话，效率会很低。

可以利用 TestNG 中的组的概念，为所有的单元测试标记成单元测试组，系统测试标记成系统测试组，然后在 Maven 指定执行哪些组达到灵活执行必要的测试案例效果，提高测试效率。具体样例代码如下所示。

1）使用 Test 标签对测试案例分组。

@Test(groups={“unit”})  // 指定 unit 组的测试方法
@Test(groups={“integration”})  // 指定 integration 组的测试方法

2）Maven 配置

```

<project>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.16</version>
                <configuration>
                    <groups>unit</groups>
                </configuration>
            </plugin>
        </plugins>
        <profiles>
            <profile>
                <id>full</id>
                <build>
                    <plugins>
                        <plugin>
                            <groupId>org.apache.maven.plugins</groupId>
                            <artifactId>maven-surfire-plugin</artifactId>
                            <version>2.16</version>
                            <configuration>
                                <groups>unit, integration</groups>
                            </configuration>
                        </plugin>
                    </plugins>
                </build>
            </profile>
        </profiles>
    </build>
</project>
```

如上代码所示，在 maven-surefire-plugin 中配置了默认执行 unit 测试组。如果要执行全面测试的话，可以激活 full profile，执行 unit 和 integration 两个测试组。