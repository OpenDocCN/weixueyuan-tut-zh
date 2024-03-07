# JSP useBean 动作

<jsp:useBean> 动作标记用于在 JSP 页面中创建 bean 实例，并且通过设置相关属性，可以将该实例存储到指定的范围。如果在指定的范围已经存在该 bean 实例，那么将使用这个实例，而不会重新创建。

实际工程中，常用 JavaBean 做组件开发，而在 JSP 页面中，只需要声明并使用这个组件，较大程度地实现了静态内容和动态内容的分离。

声明 JavaBean 的语法格式如下：

<jsp:useBean id="变量名" scope="page|request|session|application"
{
type="数据类型"
|class="package.className"
|class="package.className" type="数据类型"
|beanName="package.className" type="数据类型"
}
/>
<jsp:setProperty name="变量名" property="*" />

也可以在标记体内嵌入子标记，例如：

<jsp:useBean id="变量名" scope="page|request|session|application"...>
<jsp:setProperty name="变量名" property="*" />
</jsp:useBean>

以上两种使用方法是有严格区别的，当在页面中使用 <jsp:useBean> 标记创建一个 bean 时，对于第二种使用格式，如果该 bean 是第一次被实例化，那么标记体内的内容会被执行；如果已经存在指定的 bean 实例，则标记体内的内容就不再被执行。

对于第一种使用格式，无论在指定的范围内是否己经存在指定的 bean 实例，<jsp:useBean> 标记后面的内容都会被执行。

下面对 <jsp:useBean> 动作中各属性的用法进行详细介绍。

(1) id 属性：在 JSP 中给这个 bean 实例取的名字，即指定一个变量，只要在它的有效范围内，均可使用这个名称来调用它。该变量必须符合 Java 中变量的命名规则。

(2) scope 属性：设置所创建的 bean 实例的有效范围，取值有 4 种：page、request、session、application。默认情况下取值为 page。

*   值为 page：在当前 JSP 页面及当前页面以 include 指令静态包含的页面中有效。
*   值为 request：在当前的客户请求范围内有效。在请求被转发至的目标页面中，如果要使用原贡面中创建的 bean 实例，通过 request 对象的 getAttribute("id 属性值") 方法来获取。请求的生命周期是从客户端向服务器发出请求开始，到服务器响应这个请求给用户后结束。所以请求结束后，存储在其中的 bean 实例也就失效了。
*   值为 session：对当前 HttpSession 内的所有页面都有效。当用户访问 Web 应用程序时，服务器为用户创建一个 session 对象，并通过 session 的 ID 值来区分不同的用户。针对某一个用户而言，对象可被多个页面共享。通过 session 对象的 getAttribute(“id 属性值”) 方法获取存储在 session 中的 bean 实例。
*   值为 application:所有用户共享这个 bean 实例。有效范围从服务器启动开始，到服 务器关闭结束。application 对象是在服务器启动时创建的，可以被多个用户共享。所以，访 问 application 对象的所有用户共享存储于该对象中的 bean 实例。使用 application 对象的 getAttribute(“id 属性值”)方法获取存在于 application 对象中的 bean 实例。

Scope 属性之所以很重要，是因为只有在不存在具有相同 id 和 scope 的对象时， <8 卩:!⁸⁶&11>才会实例化新的对象；如果已有 id 和 scope 都相同的对象，则直接使用已有的 对象，此时，jspruseBean 开始标记和结束标记之间的任何内容都将被忽略。

(3) type="数据类型"：设置由 id 属性指定的 bean 实例的类型。该属性可指定要创建实例的类的本身、类的父类或者一个接口。

通过 type 属性设置 bean 实例类型的格式如下：

<jsp:useBean id="stu" type="com.Bean.StudentInfo" scope="session" />

如果在 session 范围内，名为 stu 的实例已经存在，则将该实例转换为 type 属性指定的 StudentInfo 类型（此时的类型转换必须是合法的）并赋值给 id 属性指定的变量；若指定的实例不存在，将会拋出“bean stu not found within scope”异常。

(4) class="package.className"：该属性指定了一个完整的类名，其中，package 表示类包的名字，className 表示类的 class 文件名称。

通过 class 属性指定的类不能是抽象的，它必须具有公共的、没有参数的构造方法。在没有设置 type 属性时，必须设置 class 属性。例如，通过 class 属性定位一个类的格式如下：

<jsp:useBean id="stu" class="com.Bean.StudentInfo" scope="session" />

程序首先会在 session 范围中查找是否存在名为 stu 的 StudentInfo 类的实例，如果存在，就会通过 new 操作符实例化 StudentInfo 类来获取一个实例，并以 stu 为实例名称存储在 session 范围内。

(5) class="package.className" type="数据类型"：class 属性与 type 属性可以指定同一个类，这两个属性一起使用时的格式举例说明如下：

<jsp:useBean id="stu" class="com.Bean.StudentInfo" type="com.Bean.StudentBase" scope="session" />

(6) beanName="package.className" type="数据类型"：beanName 属性与 type 属性可以指定同一个类，这两个属性一起使用时的格式举例说明如下：

<jsp:useBean id="stu" beanName="com.Bean.StudentInfo" type="com.Bean.StudentBase" />

假设 StudentBase 类为 StudentInfo 类的父类。执行到该标记时，首先，程序会创建一个以 id 属性值为名称的变量 stu，类型为 type 属性的值，并初始化为 null；然后在 session 范围内查找名为 stu 的 bean 实例。

如果实例存在，则将其转换为 type 属性指定的 StudentBase 类型（此时的类型转换必须是合法的）并赋值给变量 stu；如果实例不存在，则将通过 instantiate() 方法，从 StudentInfo 类中实例化一个类，并将其转换成 StudentBase 类型后赋值给变量 stu，最后将变量 stu 存储在 session 范围内。

一般情况下，使用如下格式来应用 <jsp:useBean> 标记：

<jsp:useBean id="变量名" class="package.className" />

如果多个页面中共享这个 bean 实例，可将 scope 属性设置为 session。

使用 <jsp:useBean> 标记在页面中实例化 bean 实例后，设置或修改该 bean 中的属性就可以用 <jsp:setProperty> 完成，读取该 bean 中指定的属性要用 <jsp:getProperty> 来完成，这两个标记在下面小节中将陆续介绍。

当然，读取和设置 bean 中的属性还有另一种方式，就是在脚本程序中利用 id 属性所命名的对象变量，通过调用该对象的方法显式地读取或者修改其属性。