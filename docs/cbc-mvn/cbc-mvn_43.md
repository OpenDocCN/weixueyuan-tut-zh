# Maven 自定义插件的 Mojo 标记和参数

> 原文：[`c.biancheng.net/view/5295.html`](http://c.biancheng.net/view/5295.html)

前面实现了简单自定义 Maven 插件的编写和测试，在代码中用到了 @goal 和 @parameter，分别用来标记插件的目标和参数。接下来详细介绍编写 Maven 插件要用到的标记。

## Mojo 标记

自定义 Maven 插件的常用标记如下所示。

#### 1）@goal<name>

这是自定义 Maven 插件 Mojo 代码中唯一必须声明的标记，用来声明该 Mojo 的目标名称。

#### 2）@phase<name>

声明默认将该目标绑定到 default 生命周期的某个阶段。这样在配置使用该插件目标时就可以不声明 phase。

#### 3）@requiresDependecyResolution<scope>

声明运行该 Mojo 之前必须解析哪些范围的依赖。比如 maven-surefire-plugin 的 test 目标中，就用 @requireDependecyResolution test 标注必须解析完测试访问的所有依赖（compile、test 和 runtime）。该标记的默认值是 runtime。

#### 4）@requiresProject<true/false>

声明该目标是不是必须在一个 Maven 项目中运行，默认值是 true。

大部分 Maven 插件的目标都需要依赖一个项目才能执行。但有例外，比如 maven-help-plugin 插件中的 system 目标，是用来显示系统属性和环境变量属性信息的，就不必要强制依赖一个项目才能运行，所以就用 @requiresProject false 声明。

#### 5）@requiresDirectInvoction<true/false>

声明该目标是否只能使用命令行调用，默认值是 false，既可以在命令行中调用，也可以在 pom 中配置绑定生命周期阶段。如果是 true 的话，就只支持在命令行中执行；如果在 pom 中进行配置绑定生命周期阶段的话，Maven 就会异常。

#### 6）@requiresOnline<true/false>

声明 Maven 是不是必须是在线状态，默认值是 false。

#### 7）@requiresReport<true/false>

声明是否要求项目报告已经生成，默认值是 false。

#### 8）@aggregator

在多模块的 Maven 项目中，声明该目标是否只在顶层模块构建的时候执行。如 maven-javadoc-plugin 的 aggregator-jar 就使用 @aggregator 标记，它只会在顶层项目生成一个已经聚合的 JavaDoc 文档。

#### 9）@execute goal＝＂<goal>＂

声明执行该目标之前，先执行指定的目标。

如果该目标是自己插件的另外一个目标，直接 goal＝＂目标名＂。

如果该目标是另外一个插件的目标，就需要写成 goal＝＂目标前缀：目标名＂。

#### 10）@execute phase＝＂<phase>＂

声明在执行该目标之前，Maven 先运行到当前生命周期的指定阶段。

#### 11）@execute lifecycle＝＂<lifecycle>＂phase＝＂<phase>＂

声明在执行该目标之前，Maven 运行到指定生命周期的指定阶段。

## Mojo 参数

在 Mojo 中，一般都会有一个或多个 Mojo 参数需要配置，会用 @parameter 标记。

Maven 执行 Boolean、Int、Float、String、Date、File 和 URL 等单值类型的参数，多值类型的参数包括数组、Collection、Map、Properties 等。下面分别介绍它们的配置。

#### 1\. Boolean（boolean、Boolean）

标记形式：

/**
*@parameter
*/
private boolean testBoolean;

pom 中的配置：

<testBoolean>value</testBoolean>

#### 2\. Int（Integer、long、Long、short、Short、byte、Byte）

标记形式：

/**
*@parameter
*/
private int testInt;

pom 中的配置：

<testInt>value</testInt>

#### 3\. Float（Float、double、Double）

标记形式：

/**
*@parameter
*/
private double testDouble;

pom 中的配置：

<testDouble>value</testDouble>

#### 4\. String（StringBuffer、char、Character）

标记形式：

/**
*@parameter
*/
private String testString;

pom 中的配置：

<testString>value</testString>

#### 5\. Date（yyyy-MM-dd hh:mm:ssa）

标记形式：

/**
*@parameter
*/
private Date testDate;

pom 中的配置：

<testDate>value</testDate>

#### 6\. File

标记形式：

/**
*@parameter
*/
private File testFile;

pom 中的配置：

<testFile>value</testFile>

#### 7\. URL

标记形式：

/**
*@parameter
*/
private URL testUrl;

Pom 中的配置

<testUrl>value</testUrl>

#### 8\. 数组

标记形式：

/**
*@parameter
*/
Private String[] test

pom 中的配置

<includes>
    <include>abc</include>
    <include>efg</include>
</includes>

#### 9\. Collection

标记形式：

/**
*@parameter
*/
Private String[] tests

pom 中的配置

<tests>
    <test>abc</test>
    <test>efg</test>
</tests>

#### 10\. Map

标记形式：

/**
*@parameter
*/
Private Map test

pom 中的配置

<test>
    <key1>value1</key1>
    <key2>value2</key2>
</test>

#### 11\. Properties

标记形式：

/**
*@parameter
*/
Private Properties tests;

Pom 中的配置：

<tests>
    <property>
        <name>name1</name>
        <value>value1</value>
    </property>
    <property>
        <name>name2</name>
        <value>value2</value>
    </property>
</tests>

除了直接使用 @parameter 标记配置的 Mojo 属性外，我们还可以使用 @parameter 的其它属性，进行进一步的详细声明：

@parameter alias="<aliasName>"

通过 alias 指定 Mojo 参数的别名。

/**
*@parameter alias="username"
*/
Private String testUserName;

Pom 中的配置：

<username>value</username>
@parameter expression="${attributeName}"

读取属性的值给参数赋值。如：

/**
*@parameter expression="${userName}"
*/
Private String testUserName

我们可以通过在 pom 中配置 userName 参数赋值，也可以在命令行中使用 -DuserName=value 进行赋值。

@parameter default-value="value/${attributeName}"

支持对 Mojo 属性赋予初始值

除了 @parameter 标记外，我们还可以使用 @required 和 @readonly 配合标记属性
1）@required 表示 Mojo 参数是必须的，如果使用了该标记，则必须配置 Mojo 值，否则会报错
2）@readonly 表示 Mojo 参数是只读的，用户不能通过配置修改。

## Maven 插件中的错误处理和日志

在 Mojo 的执行方法后面，支持 MojoExecutationException 和 MojoFailureException 两种异常。

如果运行抛出 MojoFailureException 异常，会显示“Build Failure”错误信息，表示可以预期的错误。

如果运行抛出 MojoExecutationException 异常，会显示“Build Error”错误信息，表示未预期的错误。

除了前面的两个异常定义外，Mojo 中还提供了相关方法可以输出不同等级的日志。用户可以通过这些日志输出，更详细地把握执行状况。

这里可以调用父类 AbstractMojo 的 getLog() 方法获取 Log 对象，输入四个等级的日志信息。从低到高分别是：

*   Debug：调试日志。
*   Info：消息日志。
*   Warn：警告日志。
*   Error：错误日志。

为了输出上面各个级别的信息，分别提供了三种方法。

*   Void debug（CharSequence ch）。
*   Void debug（CharSequence ch,Throwable error）。
*   Void debug（Throwable error）。