# Android Intent：不同应用程序之间通信的桥梁

> 原文：[`c.biancheng.net/view/3061.html`](http://c.biancheng.net/view/3061.html)

手机的基本功能是打电话和发短信。接下来我们会通过 Intent 的使用来介绍在 Android 系统下如何对电话和短信应用程序进行开发。

通过 Intent，程序员可以方便地将自己开发的应用程序与手机中的其他应用组件进行交互。

Intent 被译作“意图”，在 Android 中提供了 Intent 机制来协助应用间的交互与通信。

Intent 负责对应用中一次操作的动作、动作涉及数据、附加数据进行描述，Android 则根据此 Intent 的描述，负责找到对应的组件，将 Intent 传递给调用的组件，并完成组件的调用。

Intent 不仅可用于应用程序之间，也可用于应用程序内部 Activity/Service 之间的交互。因此，可以将 Intent 理解为不同组件之间通信的“媒介”，专门提供组件互相调用的相关信息。

Intent 是对它要完成的动作的一种抽象描述，Intent 封装了它要执行动作的属性：Action（动作）、Data（数据）、Category（类别）、Type（类型）、Component（组件信息）和 Extras（附加信息）。

#### 1) Action

Action 是指 Intent 要实施的动作，是一个字符串常量。如果指明了一个 Action，执行者就会依照这个动作的指示，接收相关输入，表现对应行为，产生符合条件的输出。

在 Intent 类中定义了大量的 Action 常量属性，标准的 Activity Actions 如下表所示。

**标准的 Activity Actions**

| 动作名称 | 动作功能 |
| --- | --- |
| ACTION_M AIN | 作为一个主要的进入口，而并不期望去接受数据 |
| ACTION_VIEW | 向用户去显示数据 |
| ACTION_ATTACH_DATA | 别用于指定一些数据应该附属于一些其他的地方，例如，图片数据应该附属于联系人 |
| ACTION_EDIT | 访问已给的数据，提供明确的可编辑 |
| ACTION_PICK | 从数据中选择一个子项目，并返回你所选中的项目 |
| ACTION_CHOOSER | 显示一个 activity 选择器，允许用户在进程之前选择他们想要的 |
| ACTION_GET_CONTENT | 允许用户选择特殊种类的数据，并返回（特殊种类的数据：照一张相片或录一段音） |
| ACTION_DIAL | 拨打一个指定的号码，显示一个带有号码的用户界面，允许用户去启动呼叫 |
| ACTION_CALL | 根据指定的数据执行一次呼叫 （ACTION_CALL 在应用中启动一次呼叫有缺陷，多数应用 ACTION_DIAL，ACTION_CALL 不能用在紧急呼叫上，紧急呼叫可以用 ACTION_DIAL 来实现） |
| ACTION_SEND | 传递数据，被传送的数据没有指定，接收的 action 请求用户发数据 |
| ACTION_SENDTO | 发送一条信息到指定的某人 |
| ACTION_ANSWER | 处理一个打进电话呼叫 |
| ACTION_INSERT  | 插入一条空项目到已给的容器 |
| ACTION_DELETE | 从容器中删除已给的数据 |
| ACTION_RUN | 运行数据 |
| ACTION_SYNC | 同步执行一个数据 |
| ACTION_PICK_ACTIVITY | 为以为的 Intent 选择一个 Activity，返回别选中的类 |
| ACTION_SEARCH | 执行一次搜索 |
| ACTION_WEB_SEARCH | 执行一次 web 搜索 |
| ACTION_FACTORY_TEST | 工场测试的主要进入点 |

#### 2) Data

Intent 的 Data 属性是执行动作的 URI 和 MIME 类型，不同的 Action 有不同的 Data 数据指定。

例如，通讯录中 identifier 为 1 的联系人的信息（一般以 U 形式描述），给这个人打电话的语句为：

ACTION_VIEW content://contacts/1
ACTION_DIAL content://contacts/1

#### 3) Category

Intent 中的 Category 属性起着对 Action 补充说明的作用。

通过 Action，配合 Data 或 Type 可以准确表达出一个完整的意图（加一些约束会更精准）。Intent 中的 Category 属性用于执行 Action 的附加信息。

例如，CATEGORY_LAUNCHER 表示加载程序时 Activity 出现在最上面，_HOME 表示回到 Home 界面。

#### 4) Type

Intent 的 Type 属性显示指定 Intent 的数据类型（MIME）。

通常 Intent 的数据类型可以从 Data 自身判断出来，但是一旦指定了 Type 类型，就会强制使用 Type 指定的类型而不再进行推导。

#### 5) Component

Intent 的 Compotent 属性指定 Intent 的目标组件的类名称。

通常情况下，Android 会根据 Intent 中包含的其他属性的信息进行查找，比如用 Action、Data、Type、Category 去描述一个请求，这种模式称为 Implicit Intents。通过这种模式，提供一种灵活可扩展的模式，给用户和第三方应用一个选择权。

例如，一个邮箱软件，大部分功能都不错，但是选择图片的功能不尽人意，如果采用 Implicit Intents，那么它就是一个开放的体系，如果想用手机中的其他图片代替邮箱中默认的图片，可以完成这一功能。但该模式需要付出性能上的开销，因为毕竟存在一个检索过程。于是 Android 提供了另一种模式 Explicit Intents。

该模式需要 Component 对象。Component 就是完整的类名，形如 com.xxxxx.xxxx，一旦指明就可以直接调用。

根据该属性是否被指定，Intent 可分为显式 Intent 和隐式 Intent。

#### 6) Extra

Intent 的 Extra 属性用于添加一些组件的附加信息。

比如，要通过一个 Activity 执行“发送电子邮件”这个动作请求，可以将电子邮件的 subject、body 等保存在 Extras 里，传给电子邮件发送组件。

## 显式 Intent 和隐式 Intent

Intent 寻找目标组件的方式分为两种：显式 Intent 和隐式 Intent。

显式 Intent 是通过指定 Intent 组件名称来实现的，它一般用在源组件已知目标组件名称的前提下，这种方式一般在应用程序内部实现。比如在某应用程序内，一个 Activity 启动一个 Service。

在不同应用程序之间，在不知道目标组件名称的情况下，寻找目标组件需要使用隐式 Intent。这种方式是通过 IntentFilter 实现的。

## IntentFilter

为了支持隐式 Intent，可以声明一个甚至多个 IntentFilter。每个 IntentFilter 描述组件所能响应 Intent 请求的能力。比如请求网页浏览器，网页浏览器程序的 IntentFliter 就应该声明它所希望接收的 IntentFilter Action 是 WEB_SEARCH_ACTION，以及与之相关的请求数据是网页地址 URI 格式。

如何为组件声明自己的 IntentFilter？

常见的方法是在 Android Manifest.xml 文件中用属性 <Intent-Filter> 描述组件的 IntentFilter。

一个隐式 Intent 请求要能够传递目标组件，必要通过 Action、Data 以及 Category 三方面的检查。任何一方面不匹配，Android 都不会将该隐式 Intent 传递给目标组件。接下来我们讲解这三方面检查的具体规则。

**1）动作测试**

<intent-Filter> 元素中可以包含子元素 <action>，比如：

<intent-Filter>
    <action android:name="com.example.project.SHOW-CURRENT"/>
    <action android:name="com.example.project.SHOW-RECENT"/>
    <action android:name="com.example.project.SHOW-PENDING"/>
</intent-Filter>

一条 <intent-Filter> 元素至少包含一个 <action> ，否则任何 Intent 请求都不能和该 <intent-Filter> 匹配。

如果 Intent 请求的 Action 和 <intent-Filter> 中的某一条 <action> 匹配，那么该 Intent 就通过了这条 <intent-Filter> 的动作测试。

如果 Intent 请求或者 <intent-Filter> 中没有说明具体的 Action 类型，那么就会出现下面这两种情况。

*   如果 <intent-Filter> 中没有包含任何 Action 类型，那么无论什么 Intent 请求都无法和这条 <intent-Filter> 匹配。
*   反之，如果 Intent 请求中没有设定 Action 类型，那么只要 <intent-Filter> 中包含有 Action 类型，这个 Intent 请求就将顺利通过 <intent-Filter> 的行为测试。

**2）类别测试**

<intent-Filter> 元素可以包含 <category> 子元素，比如：

<intent-Filter>
    <category android:name="android.Intent.Category.DEFALT"/>
    <category android:name="android.Intent.Category.BROWSABLE"/>
</intent-Filter>

只有当 Intent 请求中所有的 Category 与组件中某一个 Intent 的 <catetory> 完全匹配时，才会让该 Intent 请求通过测试，IntentFilter 中多余的 <category> 声明并不会导致匹配失败。

一个没有指定任何类别 Intent 请求与指定了“android.Intent.Category.DEFALT”类别的 IntentFliter 相匹配。

**3）数据测试**

数据在 <intent-Filter> 中的描述如下：

<intent-Filter>
    <data android:type="video/mpeg" android:scheme="http"……/>
    <data android:type="audio/mpeg" android:scheme="http"……/>
</intent-Filter>

<data> 元素指定了要接受的 Intent 请求的数据 URI 及数据类型，其中 URI 被分成三部分来进行匹配：scheme、authority 和 path。

用 setData() 设定的 Intent 请求的 URI 数据类型和 scheme 必须与 IntentFilter 中所指定的一致。若 IntentFilter 中还指定了 authority 或 path，它们也需要匹配才会通过测试。