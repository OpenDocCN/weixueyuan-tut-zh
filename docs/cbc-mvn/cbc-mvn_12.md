# Maven 私服的配置和使用

> 原文：[`c.biancheng.net/view/4890.html`](http://c.biancheng.net/view/4890.html)

在本地用户的“.m2”目录下，找到 settings.xml。在本文计算机上的目录是 C:\Users\leovo\.m2\settings.xml。如果计算机是第一次使用，可能没有 settings.xml 文件，不过在 apache-maven-3.3.9 的安装目录里面有个 conf/settings.xml，把这个文件复制到用户的“.m2”目录下就行。

下面就按照步骤，在 settings.xml 中完成开发员计算机同私服连接的配置。

#### 1\. 配置同服务器的认证信息

根据前面搭建的私服，应该可以得出结论。私服就是 Web 服务器，里面提供了构件资源，程序员可以通过 Web 下载。既然要连接 Web 服务器访问，首先是在本地配置能访问 Web 服务器的认证信息（用户名和密码）。

在 settings.xml 文件中找到 servers 标签，在里面添加一个 server 的认证信息配置，格式如下：

<id>archivaServer</id>
<username>admin</username>
<password>admin123</password>

注意：

*   id 是要认证的服务器名称，可以配置多个。它是用来标记服务器的，要唯一。
*   username 和 password 是用户名和密码。这里直接在初始化 Archiva 服务器的时候，创建了 admin 用户名和密码。
*   如果要连接多个私服，可以类似地配置多个 server，每个 server 是一个私服的认证信息。

#### 2\. 配置要连接的私服信息

前面在 settings.xml 中配置了连接私服的认证信息。认证信息对应的是哪个私服呢？接下来就在 settings.xml 中配置私服信息。

很简单，直接在 settings.xml 中找到 mirrors 标签，在该标签中插入如下内容。

<mirror>
    <id>archivaServer</id>
    <mirrorOf>*</mirrorOf>
    <name>MyownRepo2</name>
    <url>http://localhost:8080/repository/internal</url>
</mirror>

注：id 是私服映射的标记，该标记要同 server 中的 id 一样。如果连接私服需要认证信息，Maven 可以通过 id 找对应的 server，用 server 的认证信息进行认证。

*   mirrorOf 指定哪些内容需要通过私服下载，* 表示所有构件都需要从私服下载。
*   name 是私服的名称，随意取，方便自己记忆和理解就好。
*   url 指定私服的 Url，注意格式：

http://<私服 ip/名称>:<web 端口>/reposotory/<仓库 id>

上面的步骤比较烦琐，不过需要依赖时，可以直接从搭建的私服中获取。当然，不能保证私服有现成的。不过不要紧，私服自己会去网络中找对应的依赖，同时它也会在自己的仓库中备份，以备其他开发人员需要。

#### 3\. 配置本地工程的发布

前面将私服上的依赖下载到本地，进行项目开发了。本地的模块开发好了，怎样把它们打包，以构件的形式发布到私服上去，让同项目组的组员进一步开发其他项目或模块的功能呢？

这就是解决本地项目打包，在私服上发布成构件的问题。要完成这些功能，需要做两件事：编写配置文件和运行发布命令。

#### 1）编写配置文件

前面的步骤都是在 settings.xml 中进行配置。这步需要在工程的 pom.xml 文件中，在 project 标签内，添加 distributionManagement 配置，指定要发布的目标地（私服）。具体内容如下：

<distributionManagement>
    <repository>
    <id>archivaServer</id>
    <url>http://localhost:8080/repository/internal</url>
    </repository>
    <snapshotRepository>
        <id>archivaServer</id>
        <url>http://localhost:8080/repository/snapshots</url>
    </snapshotRepository>
</distributionManagement>

注：上面的信息配置了两个仓库 url，一个是 repository；另一个是快照 repository。每个 repository 中都有一个 url。

其中 url 就是要发布的私服仓库 url，与在 settings.xml 中配置 mirror 中的 url 一样。同样，有两个 Id。

需要注意，要与在 settings.xml 中用 server 配置的验证信息中的 Id 对应。因为发布就是上传文件，上传文件前需要安全认证。Maven 是通过 Id 将 server 中的验证信息发送给私服，私服认证通过了，才允许用户将本地构件上传。

#### 2）运行发布命令

前面已经将配置信息都配置好了，右击“工程”，选择 Run As→Maven build… 命令，在弹出框的 Goal 后面输入 deploy，单击 Run 按钮，它们就会自动发布到私服。