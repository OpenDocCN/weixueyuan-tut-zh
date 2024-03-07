# Archetype 插件的介绍和使用

> 原文：[`c.biancheng.net/view/5298.html`](http://c.biancheng.net/view/5298.html)

Maven 除了有完成构建的插件外，还有一种插件—— Archetype 插件。它的作用是生成 Maven 项目骨架（项目的目录结构和 pom.xml）。

只要给对应的 Archetype 插件提供基本的信息，比如 groupId、artifactId 和 version，它就可以生成项目的基本目录结构和 pom 文件。比如，maven-archetype-quickstart 插件就是一个快速创建简单 Maven 工程的 Archetype 插件。

有很多开源项目如 Appfuse 和 Apache Wicket 都提供了 Archetype 插件，方便开发人员快速创建项目。

当然，如果公司或项目组的 Maven 项目有自己个性化的配置和目录结构的话，也可以自定义一个 Archetype 插件发布给所有开发人员使用。

有了公共的 Archetype 插件后，不仅能让开发人员快速简单地创建 Maven 项目，而且还可以强制所有开发人员遵循统一的项目结构和配置约定，从而统一 Maven 项目的规范和标准。

Archetype 不是 Maven 的核心特征。作为插件，当用户要使用它的时候需要输入完整的插件坐标，由 Maven 根据坐标下载对应的插件运行。虽然它只是一个插件，因为使用得比较广泛，主要的 IDE（Eclipse、NetBeans 和 IDEA）在集成 Maven 的时候，都集成了 Archetype，以方便开发人员快速创建 Maven 项目。

Archetype 插件的使用比较简单。如果使用的是 IDE，比如 Eclipse，直接基于向导界面，就可以引导选择和使用对应的 Archetype 插件创建工程。

如果使用命令行，直接在 CMD 命令行窗口中输入 mvn 插件坐标就行，必要的时候再添加参数。比如使用 maven-archetype-plugin 插件创建 Maven 项目，只需输入命令如下：

Mvn archetype:generate

其中，archetype 是 maven-archetype-plugin 的简称；generate 是 maven-archetype-plugin 插件创建 Maven 项目的目标名称。

Maven 接收到 Archetype 命令后，自动到 [`maven.apache.org/archetype/maven-archetype-plugin`](http://maven.apache.org/archetype/maven-archetype-plugin) 下载最新的插件，然后运行 generate 目标，创建项目。

在中央仓库中有很多 Archetype 插件，这里对应创建简单 Maven 工程、创建 Mavenweb 工程和创建 Maven 框架工程，分别介绍一个代表。

#### 1）maven-archetype-quickstart

maven-archetype-quickstart 应该是最常用的 Archetype。在用户输入命令行“mvn archetype:generate”时，如果没有指定使用哪个 Archetype，默认就是使用 quickstart。使用 maven-archetype-quickstart 生成的项目比较简单。

*   pom.xml，包含有 JUnit 的依赖声明。
*   src/main/java，主代码目录以及一个名为 App 的 Java 类。
*   src/main/test，测试代码目录以及一个名为 AppTest 的 JUnit 测试用例类。

如果需要创建一个全新的 Maven 项目，可以使用该 Archetype 生成项目架构，再在该架构的基础上进行对应地修改，比如添加依赖、添加 resources 目录等，从而省去手动创建 pom 以及目录结构的麻烦。

#### 2）maven-archetype-webapp

maven-archetype-webapp 是一个创建 Maven War 项目的 Archetype。它能创建一个 Web 应用的基本目录结构和必需的 web.xml。使用 maven-archetype-webapp 生成如下内容。

*   pom.xml packaging 的值为 war，带有 JUnit 的依赖声明。
*   src/main/webapp 目录。
*   src/main/webapp/index.jsp 文件。
*   src/main/webapp/WEB-INF/web.xml 文件。

#### 3）AppFuse Archetype

AppFuse 是一个集成了很多开源工具的项目，它能快速高效地创建 Maven 项目。目前，AppFuse 已经集成了最流行的开源工具，比如，Spring、Struts、JPA、Hibernate、MyBatis 等。

AppFuse 提供了大量的 Archetype 方便用户创建各种类型的项目。针对不同的显示层框架，可以分为以下几种类型。

*   appfuse-*-jsf：基于 JSF 的 Archetype。
*   appfus-*-spring：基于 SpringMVC 的 Archetype。
*   appfuse-*-struts：基于 Struts2 的 Archetype。
*   appfuse-*-tapestry：基于 Tapestry 的 Archetype。

每一种 Archetype 又分 3 个 Archetype，分别为 light、basic 和 modular。

*   light 只包含简单的骨架。
*   basic 包含一些用户管理及安全方面的特性。
*   modular 会生成多模块的项目。其中，core 模块包含持久层和业务逻辑层代码；web 模块为 view 层代码。