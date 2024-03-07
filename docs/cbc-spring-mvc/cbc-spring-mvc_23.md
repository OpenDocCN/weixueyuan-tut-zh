# Spring MVC JSR-303 验证框架之 Hibernate-Validator

> 原文：[`c.biancheng.net/view/4450.html`](http://c.biancheng.net/view/4450.html)

对于 JSR 303 验证，目前有两个实现，一个是 Hibernate Validator，一个是 Apache BVal。本教程采用的是 Hibernate Validator，注意它和 Hibernate 无关，只是使用它进行数据验证。

#### 1\. 下载与安装 Hibernate Validator

用户可以通过地址“https://sourceforge.net/projects/hibernate/files/hibernate-validator/”下载 Hibernate Validator，本教程选择的是 hibernate-validator-4.3.2.Final-dist.zip。

首先将下载的压缩包解压，然后将 \hibernate-validator-4.3.2.Final\dist 目录下的 hibernate-validator-4.3.2.Final.jar 和 \hibernate-validator-4.3.2.Final\dist\lib\required 目录下的 jboss-logging-3.1.0.Final.jar、validation-api-1.0.0\. GA.jar 复制到应用的 \WEB-INF\lib 目录下。

#### 2\. 配置属性文件与验证器

如果将验证错误消息放在属性文件中，那么需要在配置文件中配置属性文件，并将属性文件与 Hibernate Validator 关联，具体配置代码如下：

```

<!-- 配置消息属性文件 -->
<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
    <!-- 资源文件名 -->
    <property name="basenames">
        <list>
            <value>/WEB-INF/resource/errorMessages</value>
        </list>
    </property>
    <!-- 资源文件编码格式 -->
    <property name="fileEncodings" value="utf-8" />
    <!-- 对资源文件内容缓存的时间，单位为秒 -->
    <property name="cacheSeconds" value="120" />
</bean>
<!-- 注册校验器 -->
<bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
    <!-- hibernate 校验器 -->
    <property name="providerClass" value="org.hibernate.validator.HibernateValidator" />
    <!-- 指定校验使用的资源文件，在文件中配置校验错误信息，如果不指定则默认使用 classpath 下的 ValidationMessages.properties -->
    <property name="validationMessageSource" ref="messageSource" />
</bean>
<!--开启 Spring 的 Valid 功能 -->
<mvc:annotation-driven conversion-service="conversionService" validator="validator" />
```

#### 3.  标注类型

JSR 303 不需要编写验证器，但需要利用它的标注类型在领域模型的属性上嵌入约束。

#### 1）空检查

*   @Null：验证对象是否为 null。
*   @NotNull：验证对象是否不为 null，无法检查长度为 0 的字符串。
*   @NotBlank：检查约束字符串是不是 null，以及被 trim 后的长度是否大于 0，只针对字符串，且会去掉前后空格。
*   @NotEmpty：检查约束元素是否为 null 或者是 empty。

示例如下：

@NotBlank(message="{goods.gname.required}") //goods.gname.required 为属性文件的错误代码
private String gname;

#### 2）boolean 检查

*   @AssertTrue：验证 boolean 属性是否为 true。
*   @AssertFalse：验证 boolean 属性是否为 false。

示例如下：

@AssertTrue
private boolean isLogin;

#### 3）长度检查

*   @Size（min=，max=）：验证对象（Array、Collection、Map、String）长度是否在给定的范围之内。
*   @Length（min=，max=）：验证字符串长度是否在给定的范围之内。

示例如下：

@Length(min=1,max=100)
private String gdescription;

#### 4）日期检查

*   @Past：验证 Date 和 Callendar 对象是否在当前时间之前。
*   @Future：验证 Date 和 Calendar 对象是否在当前时间之后。
*   @Pattern：验证 String 对象是否符合正则表达式的规则。

示例如下：

@Past(message="{gdate.invalid}")
private Date gdate;

#### 5）数值检查

| 名称 | 说明 |
| @Min | 验证 Number 和 String 对象是否大于指定的值 |
| @Max | 验证 Number 和 String 对象是否小于指定的值 |
| @DecimalMax | 被标注的值必须不大于约束中指定的最大值，这个约束的参数是一个通过 BigDecimal 定义的最大值的字符串表示，小数存在精度 |
| @DecimalMin | 被标注的值必须不小于约束中指定的最小值，这个约束的参数是一个通过 BigDecimal 定义的最小值的字符串表示，小数存在精度 |
| @Digits | 验证 Number 和 String 的构成是否合法 |
| @Digits（integer=，fraction=） | 验证字符串是否符合指定格式的数字，integer 指定整数精度，fraction 指定小数精度 |
| @Range（min=，max=） | 检查数字是否介于 min 和 max 之间 |
| @Valid | 对关联对象进行校验，如果关联对象是个集合或者数组，那么对其中的元素进行校验，如果是一个 map，则对其中的值部分进行校验 |
| @CreditCardNumber | 信用卡验证 |
| @Email | 验证是否为邮件地址，如果为 null，不进行验证，通过验证 |

示例如下：

@Range(min=10,max=100,message="{gprice.invalid}")
private double gprice;

由于篇幅有限，大家可结合《Spring MVC Hibernate-Validator 数据验证实例》教程学习。