# Django 模板中校验用户身份与权限

> 原文：[`c.biancheng.net/view/8056.html`](http://c.biancheng.net/view/8056.html)

在上一节《Django 自定义认证后端实现多种登录方式验证》中我们解决了用户登录多种实现多种验证的问题在 Django 的用户认证系统还提供了其他 API 接口，它们既方便又快捷。所以在本节中，我们将介绍 Django 用户认证系统的其他应用。

## 1\. 登录时实现用户校验认证

User 的实例对象拥有 is_authenticated() 方法，可以在用户登录时进行认证。如果是真正的 User 对象，返回值为 True，用于检查用户是否已经通过了认证。但是通过认证并不意味着用户认证拥有任何权限，甚至也不会检查该用户是否处于激活状态，这只是表明用户成功的通过了认证。

is_authenticated() 这个方法很重要，在后台用 request.user.is_authenticated() 判断用户是否已经登录，如果 True 则可以向前台展示 request.user.name 用户名，即实现欢迎某某用户登录的业务场景。使用该方法的时候应满足一下要求：用户登录后才能访问某些页面；如果用户没有登录就访问该页面的话直接跳到登录页面；用户在跳转到登录界面中完成登录后，自动跳转到之前访问的 url。

## 2\. 实现模板中校验用户身份

在模板中我们同样也可以使用 is_authenticated() 校验用户是否登录，对于已登录的用户返回是 True，反之则会 False，所以在模板中，我们可以根据这个方法的返回值来决定渲染什么样的内容，如下所示：

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>auth 用户认证</title>
</head>
<body>
{% if user.is_authenticated %}
<h1> Hello {{user.username}} Login </h1>
{% else %}
<h1>Visitor Login</h1>
{% endif %}
</body>
</html>
```

此时有个地方需要大家注意一下，此时 user 变量实例对象需要上下文进行传递，也可以用 locals() 方法或者 RequestContext 参数进行传递。

## 3\. 实现模板中校验用户权限

我们可以在模板中适应 perms 变量完成权限的校验，perms 变量是 PermWrapper 类的实例，使用它可以校验当前用户是否拥有某些权限。它可以使用如下方式导入：

from django.contrib.auth.context_processors import PermWrapper

perms 只可以完成两类权限的校验：是否有某个应用的权限，只要当前用户拥有某个应用中的任何一个权限，即为 True。使用方法见下：

{{ perms.user }}

它的返回值是当前用户被授予的 user 应用的权限，如返回值是 None 则表示当前用户没有 index 应用的权限；是否有某一项权限，这里指的是当前用户是否拥有一个确定的权限，如下所示：

{{perms.user.add_book }}

它的返回值若为 True 则代表当前用户拥有 user 应用中的 add_book 权限。在 Django 模板语言中我们可以使用下列方式来判断用户是否有某项权限：

{% if "user" in perms %}

校验当前用户是否有 user 应用的权限，假如返回了 True，则代表至少有一个 post 应用的权限。

{% if "user.add_book" in perms %}

判断当前用户是否有 user 应用的 add_book 权限。下面我们实现一个简单的模板，可以根据登录用户拥有的权限渲染不同的内容。定义 authmodel.html 文件，编写如下代码：

```

{% if perms.index %}
<p> {{ user.username }}拥有 user 应用的权限</p>>
    {% if perms.index.add_book %}
    <p>你拥有书籍添加的功能</p>
    {% endif %}
    {% if perms.index.change_book %}
    <p>你有用书籍删除的功能</p>
    {% endif %}
{% else %}
<p>关于 index 应用的权限你一个也没有拥有</p>
{% endif %}

```

从模板中我们可以看出，它首先判断当前用户是否拥有 index 应用的权限，然后再去判断是否有 add_book 和 change_book 的权限。可以将这个模板应用到视图函数中，以此来查看不同登录用户，得到的不同返回结果，注意在这个过程中，要设置不同用户的不同权限配置。