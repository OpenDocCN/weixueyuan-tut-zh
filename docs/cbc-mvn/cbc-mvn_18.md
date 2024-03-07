# Maven 使用 GPG 对文件进行签名加密

> 原文：[`c.biancheng.net/view/4849.html`](http://c.biancheng.net/view/4849.html)

每次手动对 Maven 构件进行签名，并将签名部署到 Maven 仓库中去是一种很无聊且没有技术含量的工作。为了从这种重复性的工作中解放出来，Maven 提供了一种叫 GPG 的插件来解决这个问题。用户只需在 pom.xml 中做对应的配置，例如：

```

<project>
    ...
    <plugins>
        ...
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-gpg-plugin</artifactId>
            <version>1.6</version>
            <executions>
                <execution>
                    <id>signArtifact</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>sign</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</project>
```

配置好后，使用 Mvn 命令就可以完成签名并且发布了。当然有个前提，那就是 GPG 需要安装好，也就是说，能在命令行中执行 GPG 命令。

当然，在实际项目过程中，对日常的 SNAPSHOT 构件进行签名就没有太大意义了，而且耗费资源。那有什么办法可以避免这点，只在版本正式发布的时候签名呢？

当然是可以的，在 pom 中有个 release-profile。该 profile 只有在 Maven 属性 performRelease 为 true 的时候才会被激活，而 release:perform 执行时，会把该属性的值设置成 true，这个时机刚好是项目进行版本发布的时机。

所以，用户可以在 settings.xml 或 pom 中创建如下代码，实现只是在发布正式版本的时候，对正式版本进行签名。

```

<profiles>
    <profile>
        <id> release-sign-artifacts</id>
        <activation>
            <property>
                <name>performRelease</name>
                <value>true</value>
            </property>
        </activation>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven,plugins
                    </groupId>
                    <artifactId>maven-gpg-plugin</artifactId>
                    <version>1.6</version>
                    <executions>
                        <execution>
                            <id>signArtifact</id>
                            <phase>verify</phase>
                            <goals>
                                <goal>sign</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```

需要注意的是，因为 Maven Release Plugin 有个漏洞，release:perform 执行过程中签名可能会导致进程永久挂起。为了避免这种情况发生，可以在 Maven Release Plugin 中提供一个 mavenExecutorId 配置，整体样例配置代码如下：

```

<build>
    <plugins>
        <plugin>
            <groupId> org.apache.maven.plugins</groupId>
            <artifactId>
                maven-release-plugin
            </artifactId>
            <version>2.5.3</version>
            <configuration>
                <tagBase>https://Noble-PC:8443/svn/MvnDemoSSM/tags/svnDemo
                </tagBase>
                <branchBase>https://Noble-PC:8443/svn/MvnDemoSSM/
                    branches/svnDemo
                </branchBase>
                <username>noble</username>
                <password>noble</password>
                <mavenExecutorId> forked-path</mavenExecutorId>
            </configuration>
        </plugin>
        <plugin>
            <groupId> org.apache.maven.plugins</groupId>
            <artifactId>maven-gpg-plugin</artifactId>
            <version>1.6</version>
            <executions>
                <execution>
                    <id> signArtifact</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>sign</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

到这里自动签名的配置就完成了。当 Maven 执行 release:perform 发布项目版本的时候，maven-gpg-plugin 就会自动对构件进行签名。在执行的过程中，会提示输入私钥的密码。