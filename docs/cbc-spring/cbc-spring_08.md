# Spring Bean 的配置及常用属性

> 原文：[`c.biancheng.net/view/4254.html`](http://c.biancheng.net/view/4254.html)

作为 Spring 核心机制的依赖注入，改变了传统的编程习惯，对组件的实例化不再由应用程序完成，转而交由 Spring 容器完成，在需要时注入应用程序中，从而对组件之间依赖关系进行了解耦。这一切都离不开 Spring 配置文件中使用的 <bean> 元素。

Spring 容器可以被看作一个大工厂，而 Spring 容器中的 Bean 就相当于该工厂的产品。如果希望这个大工厂能够生产和管理 Bean，这时则需要告诉容器需要哪些 Bean，以及需要以何种方式将这些 Bean 装配到一起。

Spring 配置文件支持两种不同的格式，分别是 XML 文件格式和 Properties 文件格式。

通常情况下，Spring 会以 XML 文件格式作为 Spring 的配置文件，这种配置方式通过 XML 文件注册并管理 Bean 之间的依赖关系。

XML 格式配置文件的根元素是 <beans>，该元素包含了多个 <bean> 子元素，每一个 <bean> 子元素定义了一个 Bean，并描述了该 Bean 如何被装配到 Spring 容器中。

定义 Bean 的示例代码如下所示：

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">
    <!-- 使用 id 属性定义 person1，其对应的实现类为 com.mengma.person1 -->
    <bean id="person1" class="com.mengma.damain.Person1" />
    <!--使用 name 属性定义 person2，其对应的实现类为 com.mengma.domain.Person2-->
    <bean name="Person2" class="com.mengma.domain.Person2"/>
</beans>
```

在上述代码中，分别使用 id 和 name 属性定义了两个 Bean，并使用 class 元素指定了 Bean 对应的实现类。

<bean> 元素中包含很多属性，其常用属性如表 1 所示。

表 1 <bean><bean>元素的常用属性</bean>

| 属性名称 | 描述 |
| --- | --- |
| id | 是一个 Bean 的唯一标识符，Spring 容器对 Bean 的配置和管理都通过该属性完成 |
| name | Spring 容器同样可以通过此属性对容器中的 Bean 进行配置和管理，name 属性中可以为 Bean 指定多个名称，每个名称之间用逗号或分号隔开 |
| class | 该属性指定了 Bean 的具体实现类，它必须是一个完整的类名，使用类的全限定名 |
| scope  | 用于设定 Bean 实例的作用域，其属性值有 singleton（单例）、prototype（原型）、request、session 和 global Session。其默认值是 singleton |
| constructor-arg | <bean>元素的子元素，可以使用此元素传入构造参数进行实例化。该元素的 index 属性指定构造参数的序号（从 0 开始），type 属性指定构造参数的类型 |
| property | <bean>元素的子元素，用于调用 Bean 实例中的 Set 方法完成属性赋值，从而完成依赖注入。该元素的 name 属性指定 Bean 实例中的相应属性名 |
| ref | <property> 和 <constructor-arg> 等元素的子元索，该元素中的 bean 属性用于指定对 Bean 工厂中某个 Bean 实例的引用 |
| value | <property> 和 <constractor-arg> 等元素的子元素，用于直接指定一个常量值 |
| list | 用于封装 List 或数组类型的依赖注入 |
| set | 用于封装 Set 类型属性的依赖注入 |
| map | 用于封装 Map 类型属性的依赖注入 |
| entry | <map> 元素的子元素，用于设置一个键值对。其 key 属性指定字符串类型的键值，ref 或 value 子元素指定其值 |