# MyBatis 配置文件详解

> 原文：[`c.biancheng.net/view/4322.html`](http://c.biancheng.net/view/4322.html)

MyBatis 配置文件并不复杂，它所有的元素如下所示。

```

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration><!-- 配置 -->
    <properties /><!-- 属性 -->
    <settings /><!-- 设置 -->
    <typeAliases /><!-- 类型命名 -->
    <typeHandlers /><!-- 类型处理器 -->
    <objectFactory /><!-- 对象工厂 -->
    <plugins /><!-- 插件 -->
    <environments><!-- 配置环境 -->
        <environment><!-- 环境变量 -->
            <transactionManager /><!-- 事务管理器 -->
            <dataSource /><!-- 数据源 -->
        </environment>
    </environments>
    <databaseIdProvider /><!-- 数据库厂商标识 -->
    <mappers /><!-- 映射器 -->
</configuration>
```

但是需要注意的是，MyBatis 配置项的顺序不能颠倒。如果颠倒了它们的顺序，那么在 MyBatis 启动阶段就会发生异常，导致程序无法运行。

本节的任务是了解 MyBatis 配置项的作用，其中 properties、settings、typeAliases、typeHandler、plugin、environments、mappers 是常用的内容。

本章不讨论 plugin（插件）元素的使用，在进一步学习 MyBatis 的许多底层内容和设计后我们才会学习它。MyBatis 中 objectFactory 和 databaseIdProvider 不常用。

由于在一篇文章中详细讲解 MyBatis 的配置项会导致篇幅过长，所以我们分成以下几节，请大家猛击下面的链接进行阅读：

*   MyBatis 核心配置文件 properties 元素
*   MyBatis 中 settings 属性配置详解
*   MyBatis 配置 typeAliases（别名）详解