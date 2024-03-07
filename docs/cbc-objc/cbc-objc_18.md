# Foundation 框架简介

#import <Foundation/Foundation.h>的作用是引入 Foundation 框架，究竟 Foundation 框架是什么？不用不行吗？它里边包含了哪些东西呢？一起来了解一下。

**什么是框架？**

简单的理解，框架就是别人帮你搭好的一个平台，你在现有的基础上可以继续扩展。就如同是买房，建筑商帮你盖好了房。你自己在现有基础上添置家具，对新房进行装饰。

理解了框架的概念，那么说说 Foundation 框架。Foundation 框架对于 OC 语言来说就是一个基础实现。是苹果官方的工程师们编写好的供 OC 编程使用的一个框架，在这个框架的基础上，可以编写 OC 程序实现一定的功能。

**Foundation****框架**

我们首先要使用 Xcode 创建一个 OC 工程(开始都是 Hello world 小程序)，创建完成后，我们可以在 main.m 中看到这节要讲到的 Foundation 框架，通过“command+鼠标点击<Foundation/Foundation.h>”的方式，就进入了 Foundation 框架。

通过观察就会发现，其实 Foundation 框架就是一个 Foundation.h 文件。和其它.h 文件不同的是，Foundation.h 文件中包含的全是头文件(接口)。

通过前面学习的知识知道，如果要在编程过程中使用这个类，那么首先要在程序开始之前引入这个类的.h 文件，引入之后，才可以对这个类进行操作。

而 Foundation.h 文件全是其他类的.h 文件。那么就应该明白，其实 Foundation 框架就是通过一个.h 文件将苹果官方工程师们写的各个类的接口文件包裹起来，全部包含在一个.h 文件中，给这个文件起了个名字，叫 Foundation.h 文件，又由于它符合框架的定义，所以 Foundation.h 文件又叫做 Foundation 框架。

知道了 Foundation 框架是什么了，那能不能不用它呢？答案当然是不能。你要知道 Foundation 框架是很多工程师经过很多年开发又经过多方测试才推出的。如果不用 Foundation 框架，就无法使用 OC 语言编程。也许多年之后，等你称为 IT 大神之后，自己开发一个框架，到那时，也许就不用依靠 Foundation 框架了。

**Foundation****框架涉及的领域**

提供了 OC 专有基本数据类型，如 NSArray、NSString、NSDictionary 等，如：

<Foundation/NSArray.h> <Foundation/NSData.h>

<Foundation/NSDate.h>

<Foundation/NSString.h>

<Foundation/NSDictionary.h>

提供了和网络连接相关功能的接口类，如：

<Foundation/NSURLConnection.h>

<Foundation/NSURLRequest.h>

<Foundation/NSURL.h>

提供了关于本地数据存储相关功能的接口类，如：

<Foundation/NSUserDefaults.h>

提供了对文件操作相关的接口类，例如移动文件等，如：

<Foundation/NSFileManager.h>

提供了和多线程相关的接口类，如：

<Foundation/NSThread.h>

……

**“NS”****的由来**

    通过对 Foundation 框架的功能介绍，可以观察到，基本上 90％的类结构都是以“NS”开头的，在对 OC 进行介绍的时候，我们提到过乔布斯被迫离开苹果后，自己创建了一个公司－“NeSt”公司，后来被苹果收购。实际上，“NS”指的就是乔布斯创建的这个公司。

通过上面的介绍，对于 Foundation 框架应该有更加深入的了解。而在编写程序之前，应该首先将 Foundation 框架引入进我们的程序。只有有了基础，才能更好的完成我们的工作。