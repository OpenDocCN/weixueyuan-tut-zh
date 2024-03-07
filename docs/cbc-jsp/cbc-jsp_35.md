# JavaBean 工具

JavaBean 能够将重复使用的代码进行打包，应用在可视化领域。JavaBean 能够完成 Java 的图形用户界面程序设计，实现窗体、按钮、文本框等可视化界面设计。

基于 Java 语言在网络应用方面的强大开发功能，JavaBean 的模块设计越来越成熟化。这里并没有可视化的界面，所以常被称为非可视化领域，并且在服务器端表现出了卓越的性能。

非可视化的 JavaBean 又分为值 JavaBean 和工具 JavaBean。其中，值 JavaBean 严格遵循了 JavaBean 的书写规范，主要用来封装表单数据，作为信息的容器使用。下面来创建一个值 JavaBean。

【例 1】值 JavaBean 的代码如下：

```
public class User
{
    private String userName;
    private String userPass;
    public String getUserName()
    {
        return userName;
    }
    public void setUserName(String userName)
    {
        this.userName=username;
    }
    public String getUserPass()
    {
        return UserPass;
    }
    public void setUserPass(String UserPass)
    {
        this.userPass=userPass
    }
}
```

这个值 JavaBean 用来封装用户登录时表单中的用户名和密码。从代码上看，该程序还是很简单的。

工具 JavaBean 则通常用于封装业务逻辑，比如中文处理、数据库操作等。工具 JavaBean 实现了业务逻辑和页面显示的分离，提高了代码的可读性和可重用性。接着看一个工具 JavaBean 的例子。

【例 2】工具 JavaBean 的代码如下：

```
public class Tools
{
    public String changgeHTML(String value)
    {
        value=value.replace("<","&lt;");
        value=value.replace(">","&gt;");
        reture value;
    }
}
```

这个工具 JavaBean 的功能，是将字符串中的“<”和“>”转换为对应的 HTML 字符“&lt”和“&gt”。