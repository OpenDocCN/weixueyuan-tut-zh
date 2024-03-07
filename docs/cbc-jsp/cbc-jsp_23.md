# JSP setProperty 动作

<jsp:setProperty> 动作标记通常与 <jsp:useBean> 动作标记一起使用，它以请求中的参数给创建的 JavaBean 中对应的属性赋值，通过调用 bean 中的 setXxx() 方法来完成。其语法格式如下：

<jsp:useBean id="变量名"... />
{
    property="*"
    |property="propertyName"
    |property="propertyName" parme="parmeterName"
    |property="propertyName" value="值"
}
/>

下面对 <jsp:setProperty> 动作中各属性的用法进行详细介绍。

(1) name 属性：用来指定一个存在于 JSP 中某个范围内的 bean 实例。

<jsp:setProperty> 动作标记将按照 page、request、session 和 application 的顺序来查找这个 bean 实例，直到第一个实例被找到。如果任何范围内都不存在这个 bean 实例会拋出异常。

(2) property="*"：当 property 的取值为“*”时，要求 bean 属性的名称与类型要与 request 请求中参数的名称及类型一致，以便用 bean 中的属性来接收客户输入的数据，系统会根据名称来自动匹配。

如果 request 请求中存在值为空的参数，那么 bean 中对应的属性将不会被赋值为 null；如果 bean 中存在一个属性，但请求中没有与之对应的参数，那么该属性同样不会被赋值为 null。这两种情况下的 bean 属性都会保留原来的值或者默认的值。

此种使用方法的限定条件是：请求中参数的名称和类型必须与 bean 中属性的名称和类型完全一致。但通过表单传递的参数都是 String 类型，所以，JSP 会自动地将这些参数转换为 bean 中对应属性的类型。

表 1 给出了 JSP 自动将 String 类型转换为其他类型时所调用的方法。

表 1 JSP 自动将 String 类型转换为其他类型时所调用的方法

| 其他类型 | 转换方法 |
| Integer | java.lang.Integer.value()Of(String) |
| int | java.lang.Integer.value()Of( String).intValue() |
| Double | java.lang.Double.value()Of(String) |
| double | java.lang.Double.value()Of(String).doubleValue() |
| Float | java.lang.Float.value()Of(String) |
| float | jaya.lang.Float.value()Of(String).floatValue() |
| Long | java.lang.Long.value()Of(String) |
| long | java.lang.Long.value()Of( String).longValue() |
| Boolean | java.lang.Boolean.value()Of(String) |
| boolean | java.lang.Boolean.value()Of(String).booleanValue() |
| Byte | java.lang.Byte.value()Of(String) |
| byte | java.lang.Byte.value()Of(String).byteValue() |

(3) property="upropertyName"：当 property 属性取值为 bean 中的属性时，只会将 request 请求中与该 bean 属性同名的一个参数的值赋给这个 bean 属性。

如果请求中没有与 property 所指定的同名参数，则该 bean 属性会保留原来的值或默认的值，而不会被赋值为 null。与 property 属性值为时一样，当请求中参数的类型与 bean 中的属性类型不一致时，JSP 会自动进行转换。

(4) property="propertyName" param="parameterName"：property 属性指定 bean 中的某个属性，param 属性指定 request 请求中的参数。该种方法允许将请求中的参数赋值给 bean 中与该参数不同名的属性。

如果 param 属性指定参数的值为空，那么由 property 属性指定的 bean 属性会保留原来的值或默认的值，而不会被赋为 null。

(5) property="propertyName" value="值"：value 属性指定的值可以是一个字符串数值或表示一个具体值的 JSP 表达式或 EL 表达式，该值将被赋给 property 属性指定的 bean 属性。

当 value 属性是一个字符串时，如果指定的 bean 属性与其类型不一致，JSP 会将该字符串值自动转换成对应的类型。

当 value 属性指定的是一个表达式时，则该表达式所表示的值的类型必须与 property 属性指定的 bean 属性一致，否则，将会拋出 argument type mismatch 异常。