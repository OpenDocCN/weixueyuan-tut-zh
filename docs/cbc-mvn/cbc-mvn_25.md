# Maven 配置远程仓库

> 原文：[`c.biancheng.net/view/4987.html`](http://c.biancheng.net/view/4987.html)

虽然用户可以从中央仓库中找到绝大部分流行的构件，但是毕竟不能找到所有构件。对那些在中央仓库中没有的构件，又要怎么办呢？可以在 pom.xml 中添加另外一个远程仓库。比如，将 jboss Maven 远程仓库添加到 Maven，需要在 Maven 工程的 pom.xml 中添加如下配置。

```

<project>
    ...
    <repositories>
        <repository>
            <id>jboss</id>
            <name>JBoss Maven Repository</name>
            <url>http://repository.jboss.com/maven2/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <layout>default</layout>
        </repository>
    </repositories>
    ...
</project>
```

在 repositories 元素下，可以使用 repository 子元素声明一个或多个远程仓库。该例子中配置了一个 id 为 jboss，名称为 JBoss Maven Repository 的仓库。

在 pom 中可以配置多个仓库，每个仓库的 id 都要是唯一的。而且需要注意的是，在 Maven 的超级 pom 中，已经默认配置了一个中央仓库，它的 id 为 central。

所以请不要再配置一个这样的 id，否则新的配置会覆盖原来的配置。在 repository 中，有一个 URL 元素，该元素是指定当前配置的远程仓库地址，一般来说都是基于 HTTP 的。

另外，配置中的 releases 和 snapshots 元素也是比较重要的元素，它们用来控制 Maven 对发布版本的构件和快照版本的构件的下载。当它们的子元素 enabled 的值配置成 true 或 false 的时候，表示开启或关闭对应版本的构件下载。

在实际项目中，一般开启 releases 版本的构件下载，屏蔽 snapshots 版本的构件下载，因为 snapshots 版本的构件不稳定。

至于上面例子中的 layout 元素，值为 default，表示仓库布局是 Maven2 和 Maven3 的默认布局，而不是 Maven1 的布局。

当然，也可以在 releases 和 snapshots 元素中，添加 updatePolicy 和 checksumPolicy 两个子元素进一步指定仓库的控制使用。

比如：

```

<releases>
    <enabled>true</enabled>
    <updatePolicy>daily</updatePolicy>
    <checksumPolicy>ignore</checksumPolicy>
</release>
```

updatePolicy 配置 Maven 从远程仓库检测更新的频率，默认值为 daily，表示每天检测异常。此外，还可以配置其他的值：

*   never：从不检测更新。
*   always：每次构建都检测更新。
*   interfal:X：每隔 X 分钟检测一次更新。

checksumPolicy 配置检测校验和文件的策略。当构件被部署到 Maven 仓库的时候，自动会部署对应的校验和文件。在下载构件的时候，Maven 会验证校验和文件，如果失败了怎么办？

当 checksumPolicy 的值为 warn 时，Maven 会执行构建时输出警告信息；如果是 fail，Maven 会直接中止，提示失败；如果是 ignore，Maven 会忽略校验的错误，继续构建 Maven 项目。checksumPolicy 的默认值是 warn。

配置好了远程仓库，那怎样将自己的 Maven 项目构建成构件，发布到远程仓库中去呢？

需要在 pom.xml 中使用 distributionManagement 配置部署信息就可以了，样例配置如下：

```

<distributionManagement>
    ...
    <repository>
        <id>archivaServer</id>
        <name>Archiva Releases</name>
        <url>http://localhost:8080/repository/internal</url>
    </repository>
    <snapshotRepository>
        <id>archivaServer</id>
        <name>Archiva Snapshots</name>
        <url>http://localhost:8080/repository/snapshots</url>
    </snapshotRepository>
    ...
</distributionManagement>
```

distributionManagement 中包含 repository 和 snapshotRepository 两个子元素，repository 表示发布版本的构件仓库，snapshotRepository 表示快照版本的构件仓库。id 和 name 分别是仓库的唯一标记与名称。

在 pom.xml 中完成了上面类似的配置后，使用 Maven 命令 mvn deploy，Maven 就会自动将构建输出的构件部署到对应的仓库中。

不管是在远程仓库中部署构件，还是从远程仓库中下载依赖构件，实质上都是对服务器进行访问。有些服务器访问是需要权限认证的，只有认证通过后的用户才能发请求访问服务器，特别是添加、修改和删除服务器上的文件。

那怎样配置，才能让 Maven 自动访问那些需要权限认证后才能访问的远程仓库呢？

同配置仓库信息和远程私服部署信息不同，它们都是在 pom.xml 中进行配置的，需要在 settings.xml 中进行配置。因为 pom 是被提交到代码仓库中供所有成员访问的，而 settings.xml 一般只放在本地机器，因此在 settings.xml 中配置认证信息更安全。比如如下配置，就是配置的访问前面搭建的 Archiva 私服的安全认证信息：

```

<settings>
    ...
    <servers>
        ...
        <server>
            <id>archivaServer</id>
            <username>admin</username>
            <password>admin123</password>
        </server>
        ...
    </servers>
    ...
</settings>
```

其中，username 和 password 是服务器中安全认证的用户名与密码信息。id 为认证服务器的唯一标记。这个标记需要同 pom.xml 中 distributionManagement 里面配置的仓库的 id 对应起来。表示访问某个仓库的地址的话，需要先根据 id 找到 server 的认证信息认证，才能有权限访问。