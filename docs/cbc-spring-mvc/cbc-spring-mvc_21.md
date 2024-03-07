# Spring MVC 验证器：Validator 接口和 ValidationUtils 类

> 原文：[`c.biancheng.net/view/4446.html`](http://c.biancheng.net/view/4446.html)

本节主要介绍创建自定义 Spring 验证器时需要实现的 Validator 接口和工具类 ValidationUtils。

## Validator 接口

创建自定义 Spring 验证器需要实现 org.springframework.validation.Validator 接口，该接口有两个接口方法：

boolean supports(Class<?> klass)
void validate(Object object,Errors errors)

当 supports 方法返回 true 时，验证器可以处理指定的 Class。validate 方法的功能是验证目标对象 object，并将验证错误消息存入 Errors 对象。

往 Errors 对象存入错误消息的方法是 reject 或 rejectValue，这两个方法的部分重载方法如下：

void reject(String errorCode)
void reject(String errorCode,String defaultMessage)
void rejectValue(String filed,String errorCode)
void rejectValue(String filed,String errorCode,String defaultMessage)

在一般情况下只需要给 reject 或 rejectValue 方法一个错误代码，Spring MVC 框架就会在消息属性文件中查找错误代码，获取相应错误消息。具体示例如下：

if(goods.getGprice() > 100 || goods.getGprice() < 0){
    errors.rejectValue("gprice","gprice.invalid");  // gprice.invalid 为错误代码
}

## ValidationUtils 类

org.springframework.validation.ValidationUtils 是一个工具类，该类中有几个方法可以帮助用户判定值是否为空。

例如：

if(goods.getGname()==null || goods.getGname().isEmpty()) {
    errors.rejectValue("gname","goods.gname.required")
}

再如：

if(goods.getGname() == null || goods.getGname().trim().isEmpty()) {
    errors.rejectValue("gname","goods.gname.required")
}

上述 if 语句可以编写成：

//gname 为 goods 对象的属性
ValidationUtils.rejectIfEmptyOrWhitespace(errors,"gname","goods.gname.required");

由于篇幅有限，大家可参考《Spring MVC 验证器应用实例》教程巩固学习。