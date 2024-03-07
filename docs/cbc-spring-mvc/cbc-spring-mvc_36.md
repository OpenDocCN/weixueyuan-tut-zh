# JSP EL 表达式的基本语法及运算符（超详细）

> 原文：[`c.biancheng.net/view/4487.html`](http://c.biancheng.net/view/4487.html)

EL 是 JSP 2.0 规范中新增加的，它的基本语法如下：

${表达式}

EL 表达式类似于 JSP 表达式 <%=表达式%>，EL 语句中的表达式值会被直接送到浏览器显示，通过 page 指令的 isELIgnored 属性来说明是否支持 EL 表达式。

当 isELIgnored 属性值为 false 时，JSP 页面可以使用 EL 表达式，当 isELIgnored 属性值为 true 时，JSP 页面不能使用 EL 表达式。isELIgnored 属性值默认为 false。

EL 的语法简单、使用方便，它以`${`开始、以`}`结束。

#### 1\. “[]”与“．”运算符

EL 使用“[]”和“.”运算符来访问数据，主要使用 EL 获取对象的属性，包括获取 JavaBean 的属性值、获取数组中的元素以及获取集合对象中的元素。

对于 null 值直接以空字符串显示，而不是 null，在运算时也不会发生错误或空指针异常，所以在使用 EL 访问对象的属性时不需要判断对象是否为 null 对象，这样就为编写程序提供了方便。

#### 1）获取 JavaBean 的属性值

假设在 JSP 页面中有这样一句话：

<%=user.getAge()%>

那么可以使用 EL 获取 user 对象的属性 age，代码如下：

${user.age}

或

${user["age"]}

其中，点运算符前面为 JavaBean 的对象 user，后面为该对象的属性 age，表示利用 user 对象的 getAge 方法取值并显示在网页上。

#### 2）获取数组中的元素

假设在 Controller 或 Servlet 中有这样一段话：

String dogs[] = {"lili","huahua","guoguo"};
request.setAttribute("array",dogs);

那么在对应视图 JSP 中可以使用 EL 取出数组中的元素，代码如下：

${array[0]}
${array[1]}
${array[2]}

#### 3）获取集合对象中的元素

假设在 Controller 或 Servlet 中有这样一段话：

```

ArrayList<UserBean> users = new ArrayList<UserBean>();
UserBean ub1 = new UserBean("zhang",20);
UserBean ub2 = new UserBean("zhao",50);
users.add(ub1);
users.add(ub2);
request.setAttribute("array",users);
```

其中，UserBean 有两个属性 name 和 age，那么在对应视图 JSP 页面中可以使用 EL 取出 UserBean 中的属性，代码如下：

${array[0].name} ${array[0].age}
${array[1].name} ${array[1].age}

#### 2\. 算术运算符

在 EL 表达式中有 5 个算术运算符，如表 1 所示。

表 1 EL 的算术运算符

| 算术运算符 | 说 明 | 示   例 | 结   果 |
| + | 加 | ${13+2} | 15 |
| - | 减 | ${13-2} | 11 |
| * | 乘 | ${13*2} | 26 |
| /（或 div） | 除 | ${13/2}或${13 div 2} | 6.5 |
| %（或 mod） | 取模（求余） | ${13%2}或${13 mod2} | 1 |

#### 3\. 关系运算符

在 EL 表达式中有 6 个关系运算符，如表 2 所示。

表 2 EL 的关系运算符

| 关系运算符 | 说 明 | 示 例 | 结 果 |
| ==（或 eq） | 等于 | ${13==2}或${13 eq 2} | false |
| !=（或 ne） | 不等于 | ${13!=2}或${13 ne 2} | true |
| <（或 lt） | 小于 | ${13<2}或${13 lt 2} | false |
| >（或 gt） | 大于 | ${13>2}或${13 gt 2} | true |
| <=（或 le） | 小于等于 | ${13<=2}或${13 le 2} | false |
| >=（或 ge） | 大于等于 | ${13>=2}或${13 ge 2} | true |

#### 4\. 逻辑运算符

在 EL 表达式中有 3 个逻辑运算符，如表 3 所示。

表 3 EL 的逻辑运算符

| 逻辑运算符 | 说   明 | 示   例 | 结   果 |
| &&（或 and） | 逻辑与 | 如果 A 为 true，B 为 false，则 A && B（或 A and B） | false |
| &#124;&#124;（或 or） | 逻辑或 | 如果 A 为 true，B 为 false，则 A&#124;&#124;B（或 A or B） | true |
| !（或 not） | 逻辑非 | 如果 A 为 true，则 !A（或 not A） | false |

#### 5\. empty 运算符

empty 运算符用于检测一个值是否为 null，例如变量 A 不存在，则 ${empty A} 返回的结果为 true。

#### 6\. 条件运算符

EL 中的条件运算符是“? :”，例如 ${A ? B:C}，如果 A 为 true，计算 B 并返回其结果，如果 A 为 false，计算 C 并返回其结果。