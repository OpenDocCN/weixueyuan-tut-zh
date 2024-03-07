# Qt 类库模块划分详解

Qt 类库里大量的类根据功能分为各种模块，这些模块又分为以下几大类：

*   Qt 基本模块（Qt Essentials)：提供了 Qt 在所有平台上的基本功能。
*   Qt 附加模块（Qt Add-Ons)：实现一些特定功能的提供附加价值的模块。
*   增值模块（Value-AddModules)：单独发布的提供额外价值的模块或工具。
*   技术预览模块（Technology Preview Modules）：一些处于开发阶段，但是可以作为技术预览使用的模块。
*   Qt 工具（Qt Tools)：帮助应用程序开发的一些工具。

提示：Qt 官网的“All Modules”页面可以查看所有这些模块的信息。

#### Qt 基本模块

Qt 基本模块是 Qt 在所有平台上的基本功能，它们在所有的幵发平台和目标平台上都可用，在 Qt 5 所有版本上是源代码和二进制兼容的。这些具体的基本模块见表 1。

表 1 Qt 基本模块

| 模块 | 描述 |
| Qt Core | 其他模块都用到的核心非图形类 |
| Qt GUI | 设计 GUI 界面的基础类，包括 OpenGL |
| Qt Multimedia | 音频、视频、摄像头和广播功能的类 |
| Qt Multimedia Widgets | 实现多媒体功能的界面组件类 |
| Qt Network | 使网络编程更简单和轻便的类 |
| Qt QML | 用于 QML 和 JavaScript 语言的类 |
| Qt Quick | 用于构建具有定制用户界面的动态应用程序的声明框架 |
| Qt Quick Controls | 创建桌面样式用户界面，基于 Qt Quick 的用户界面控件 |
| Qt Quick Dialogs | 用于 Qt Quick 的系统对话框类型 |
| Qt Quick Layouts | 用于 Qt Quick 2 界面元素的布局项 |
| Qt SQL | 使用 SQL 用于数据库操作的类 |
| Qt Test | 用于应用程序和库进行单元测试的类 |
| Qt Widgets | 用于构建 GUI 界面的 C++ 图形组件类 |

Qt Core 模块是 Qt 类库的核心，所有其他模块都依赖于此模块，如果使用 qmake 构建项目， 则 Qt Core 模块是自动被加入项目的。

Qt GUI 模块提供了用于开发 GUI 应用程序的必要的类，使用 qmake 构建应用程序时，Qt GUI 模块是自动被加入项目的。如果项目中不使用 GUI 功能，则需要在项目配置文件中加入如下的一行：

QT -= gui

其他的模块一般不会被自动加入到项目，如果需要在项目中使用某个模块，则可以在项目配置中添加此模块。例如，如果需要在项目中使用 Qt Multimedia 和 Qt Multimedia Widgets 模块，需要在项目配置文件中加入如下的语句：

QT += multimedia multimediawidgets

需要在项目中使用 Qt SQL 模块，就在项目配置文件中加入如下的语句：

QT += sql

#### Qt 附加模块

Qt 附加模块可以实现一些特定目的。这些模块可能只在某些开发平台上有，或只能用于某些操作系统，或只是为了向后兼容。用户安装时可以选择性地安装这些附加模块。

表 2 是附加模块列表（未列出一些过时的模块，以及专门用于 QML 或 Qt Quick 的模块）。

表 2 Qt 附加模块

| 模块 | 描述 |
| Active Qt | 用于开发使用 ActiveX 和 COM 的 Windows 应用程序 |
| Qt 3D | 支持 2D 和 3D 渲染，提供用于开发近实时仿真系统的功能 |
| Qt Android Extras | 提供 Android 平台相关的 API |
| Qt Bluetooth | 提供访问蓝牙硬件的功能 |
| Qt Concurrent | 提供一些类，无需使用底层的线程控制就可以编写多线程程序 |
| Qt D-Bus | 使进程间通过 D-Bus 协议通信的一些类 |
| Qt Gamepad | 使 Qt 应用程序支持游戏手柄硬件的使用 |
| Qt Image Formats  | 支持附加图片格式的插件，包括 TIFF、MNG、TGA、WBMP |
| Qt Mac Extras | 提供 macOS 平台相关的 API |
| Qt NFC | 提供访问 NFC (近场通信）硬件的功能 |
| Qt Positioning | 提供一些类，用于通过 GPS 卫星、WiFi 等定位 |
| Qt Print Support | 提供一些用于打印控制的类 |
| Qt Purchasing | 提供一些类，在 Qt 应用程序内实现应用内购买的功能 |
| Qt Sensors | 提供访问传感器硬件的功能，以识别运动和手势 |
| Qt Serial Bus | 访问串行工业总线的功能，目前只支持 CAN 和 Modbus 协议 |
| Qt SVG | 提供显示 SVG 图片文件的类 |
| Qt WebChannd | 用于实现服务器端（QML 或 C++ 应用程序）与客户端（HTML/JavaScript 或 QML 应用程序）之间的 P2P 通信 |
| Qt WebEngine | 提供类和函数，实现在应用程序中嵌入网页内容 |
| Qt WebSocket | 提供兼容于 RFC 6455 的 WebSocket 通信，是实现客户端程序与远端主机进行双向通信的基于 Web 的协议 |
| Qt Windows Extras | 提供 Windows 平台相关的 API |
| Qt XML | 该模块不再维护了，应使用 QtCore 中的 QXmlStreamReader 和 QXmlStream Writer Qt XML Patterns 提供对 XPath、XQuery、XSLT 和 XML 等的支持 |
| Qt Charts  | 用于数据显示的二维图表组件 |
| Qt Data Visualization | 用于 3D 数据可视化显示的界面组件 |
| Qt Virtual Keyboard | 实现不同输入法的虚拟键盘框架 |

#### 增值模块

除了随 Qt 5 发布的上述这些模块，还有一些模块（见表 3)是单独发布的，这些模块只在商业版许可的 Qt 里才有。

表 3 Qt 的增值模块

| 特性 | 描述 |
| Qt for Device Creation | 高效、易用、全集成的嵌入式设备应用程序开发工具，包括很多其他增值特性 |
| Qt Quick Compiler | 编译.qml 源文件生成二进制应用程序的编译器，提高载入时间和代码的安全性 |

#### 技术预览模块

技术预览模块就是一些还处于开发和测试阶段的模块，一般技术预览模块经过几个版本的发布后会变成正式的模块。表 4 是 Qt 5.9 中的技术预览模块。

表 4 技术预览模块

| 模块 | 描述 |
| Qt Network Authorization | 基于 OAuth 协议，为应用程序提供网络账号验证的功能 |
| Qt Speech | 提供文字转语音（text-to-speech）功能支持 |
| Qt Remote Objects | 进程间或设备间通信，共享 QObject 的 API |

#### Qt 工具

Qt 工具（见表 5)在所有支持的平台上都可以使用，用于帮助应用程序的开发和设计。

表 5 Qt 工具

| 工具 | 描述 |
| Qt Designer | 用于扩展 Qt Designer 的类 |
| Qt Help | 在应用程序中集成在线文档的类，实现类似于 Qt Assistant 的功能 |
| QtUI Tools | 操作 Qt Designer 生成的窗体的类 |