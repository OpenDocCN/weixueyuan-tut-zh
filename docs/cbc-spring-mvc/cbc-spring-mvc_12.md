# Spring MVC Converter（类型转换器）详解

> 原文：[`c.biancheng.net/view/4415.html`](http://c.biancheng.net/view/4415.html)

Spring MVC 框架的 Converter<S，T> 是一个可以将一种数据类型转换成另一种数据类型的接口，这里 S 表示源类型，T 表示目标类型。开发者在实际应用中使用框架内置的类型转换器基本上就够了，但有时需要编写具有特定功能的类型转换器。

## 内置的类型转换器

在 Spring MVC 框架中，对于常用的数据类型，开发者无须创建自己的类型转换器，因为 Spring MVC 框架有许多内置的类型转换器用于完成常用的类型转换。Spring MVC 框架提供的内置类型转换包括以下几种类型。

#### 1）标量转换器

| 名称 | 作用 |
| StringToBooleanConverter | String 到 boolean 类型转换 |
| ObjectToStringConverter | Object 到 String 转换，调用 toString 方法转换 |
| StringToNumberConverterFactory | String 到数字转换（例如 Integer、Long 等） |
| NumberToNumberConverterFactory | 数字子类型（基本类型）到数字类型（包装类型）转换 |
| StringToCharacterConverter | String 到 Character 转换，取字符串中的第一个字符 |
| NumberToCharacterConverter | 数字子类型到 Character 转换 |
| CharacterToNumberFactory | Character 到数字子类型转换 |
| StringToEnumConverterFactory | String 到枚举类型转换，通过 Enum.valueOf 将字符串转换为需要的枚举类型 |
| EnumToStringConverter | 枚举类型到 String 转换，返回枚举对象的 name 值 |
| StringToLocaleConverter | String 到 java.util.Locale 转换 |
| PropertiesToStringConverter | java.util.Properties 到 String 转换，默认通过 ISO-8859-1 解码 |
| StringToPropertiesConverter | String 到 java.util.Properties 转换，默认使用 ISO-8859-1 编码 |

#### 2）集合、数组相关转换器

| 名称 | 作用 |
| ArrayToCollectionConverter | 任意数组到任意集合（List、Set）转换 |
| CollectionToArrayConverter | 任意集合到任意数组转换 |
| ArrayToArrayConverter | 任意数组到任意数组转换 |
| CollectionToCollectionConverter | 集合之间的类型转换 |
| MapToMapConverter | Map 之间的类型转换 |
| ArrayToStringConverter | 任意数组到 String 转换 |
| StringToArrayConverter | 字符串到数组的转换，默认通过“，”分割，且去除字符串两边的空格（trim） |
| ArrayToObjectConverter | 任意数组到 Object 的转换，如果目标类型和源类型兼容，直接返回源对象；否则返回数组的第一个元素并进行类型转换 |
| ObjectToArrayConverter | Object 到单元素数组转换 |
| CollectionToStringConverter | 任意集合（List、Set）到 String 转换 |
| StringToCollectionConverter | String 到集合（List、Set）转换，默认通过“，”分割，且去除字符串两边的空格（trim） |
| CollectionToObjectConverter | 任意集合到任意 Object 的转换，如果目标类型和源类型兼容，直接返回源对象；否则返回集合的第一个元素并进行类型转换 |
| ObjectToCollectionConverter | Object 到单元素集合的类型转换 |

类型转换是在视图与控制器相互传递数据时发生的。Spring MVC 框架对于基本类型（例如 int、long、float、double、boolean 以及 char 等）已经做好了基本类型转换。例如，对于 addGoods.jsp 的提交请求，可以由以下处理方法来接收请求参数并处理：

```

package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class Goodsontroller {
    @RequestMapping("/addGoods")
    public String add(String goodsname, double goodsprice, int goodsnumber) {
        double total = goodsprice * goodsnumber;
        System.out.println(total);
        return "success";
    }
}
```

注意：在使用内置类型转换器时，请求参数输入值与接收参数类型要兼容，否则会报 400 错误。请求参数类型与接收参数类型不兼容问题需要学习输入校验后才可解决。

## 自定义类型转换器

当 Spring MVC 框架内置的类型转换器不能满足需求时，开发者可以开发自己的类型转换器。

例如有一个应用 springMVCDemo03 希望用户在页面表单中输入信息来创建商品信息。当输入“apple，10.58，200”时表示在程序中自动创建一个 new Goods，并将“apple”值自动赋给 goodsname 属性，将“10.58”值自动赋给 goodsprice 属性，将“200”值自动赋给 goodsnumber 属性。

springMVCDemo03 应用与 springMVCDemo02 具有相同的 JAR 包、web.xml。

如果想实现上述应用，需要做以下 5 件事：

*   创建实体类。
*   创建控制器类。
*   创建自定义类型转换器类。
*   注册类型转换器。
*   创建相关视图。

按照上述步骤采用自定义类型转换器完成需求。

#### 1）创建实体类

在 springMVCDemo03 的 src 目录下创建 pojo 包，并在该包中创建名为 GoodsModel 的实体类，代码如下：

```

package pojo;

public class GoodsModel {
    private String goodsname;
    private double goodsprice;
    private int goodsnumber;
    // 省略 setter 和 getter 方法
}
```

#### 2）创建控制器类

在 springMVCDemo03 的 src 目录下创建 controller 包，并在该包中创建名为 ConverterController 的控制器类，代码如下：

```

package controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import pojo.GoodsModel;

@Controller
@RequestMapping("/my")
public class ConverterController {
    @RequestMapping("/converter")
    /*
     * 使用@RequestParam
     * ("goods")接收请求参数，然后调用自定义类型转换器 GoodsConverter 将字符串值转换为 GoodsModel 的对象 gm
     */
    public String myConverter(@RequestParam("goods") GoodsModel gm, Model model) {
        model.addAttribute("goods", gm);
        return "showGoods";
    }
}
```

#### 3）创建自定义类型转换器类

自定义类型转换器类需要实现 Converter<S,T> 接口，重写 convert(S) 接口方法。

convert(S) 方法的功能是将源数据类型 S 转换成目标数据类型 T。在 springMVCDemo03 的 src 目录下创建 converter 包，并在该包中创建名为 GoodsConverter 的自定义类型转换器类，代码如下：

```

package converter;

import org.springframework.core.convert.converter.Converter;
import pojo.GoodsModel;

public class GoodsConverter implements Converter<String, GoodsModel> {
    public GoodsModel convert(String source) {
        // 创建一个 Goods 实例
        GoodsModel goods = new GoodsModel();
        // 以“，”分隔
        String stringvalues[] = source.split(",");
        if (stringvalues != null && stringvalues.length == 3) {
            // 为 Goods 实例赋值
            goods.setGoodsname(stringvalues[0]);
            goods.setGoodsprice(Double.parseDouble(stringvalues[1]));
            goods.setGoodsnumber(Integer.parseInt(stringvalues[2]));
            return goods;
        } else {
            throw new IllegalArgumentException(String.format(
                    "类型转换失败， 需要格式'apple, 10.58,200 ',但格式是[% s ] ", source));
        }
    }
}
```

#### 4）注册类型转换器

在 springMVCDemo03 的 WEB-INF 目录下创建配置文件 springmvc-servlet.xml，并在配置文件中注册自定义类型转换器，配置文件代码如下：

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 使用扫描机制扫描控制器类，控制器类都在 controller 包及其子包下 -->
    <context:component-scan base-package="controller" />
    <!--注册类型转换器 GoodsConverter-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <list>
                <bean class="converter.GoodsConverter"/>
            </list>
        </property>
    </bean>
    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

#### 5）创建相关视图

在 springMVCDemo03 应用的 WebContent 目录下创建信息采集页面 input.jsp，核心代码如下：

<form action="${pageContext.request.contextPath}/my/converter" method= "post">
    请输入商品信息（格式为 apple, 10.58,200）:
    <input type="text" name="goods" /><br>
    <input type="submit" value="提交" />
</form>

在 springMVCDemo03 应用的 /WEB-INF/jsp 目录下创建信息显示页面 showGoods.jsp，核心代码如下：

```

<body>
    您创建的商品信息如下：
    <!-- 使用 EL 表达式取出 model 中的 goods 信息 -->
    商品名称为:${goods.goodsname }
    商品价格为:${goods.goodsprice }
    商品名称为:${goods.goodsnumber }
</body>
```

最后，使用地址“ http://localhost：8080/springMVCDemo03 /input.jsp ”测试应用。