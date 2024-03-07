# Django 项目创建第一个应用

> 原文：[`c.biancheng.net/view/7505.html`](http://c.biancheng.net/view/7505.html)

Django 项目就是基于 Django 框架开发的 Web 应用，它包含了一组配置和多个应用，我们把应用称之为 App，在前文中对它也做了相应的介绍，比如 auth、admin，它们都属于 APP。

一个 App 就是一个 Python 包，通常一个 App 可以包含模型、视图、模板和 URL 配置文件，可以被应用到多个 Django 项目中，因为它们的本质就是可被重用的 Python 软件包。

## 1\. 创建应用

Django 的设计目标是让开发者关注应用的功能逻辑的实现，所以，创建应用的过程是非常简单的，利用 manage.py 提供的 startapp 命令就可以创建一个 APP，具体命令如下所示：

python manage.py startapp index

startapp 同样也属于 manage.py 的子命令，用来创建 Django 的应用。执行这个命令不会在 CMD 命令行看到任何输出，但是，可以在 manage.py 的同级目录下看到多出了一个 index 目录，我们使用 `tree` 命令来查看 index 的目录结构如下所示：

```

C:\Users\Administrator\Book\BookStore>python manage.py startapp index
C:\Users\Administrator\Book\BookStore>tree /f index
C:\USERS\ADMINISTRATOR\BOOK\BOOKSTORE\INDEX
├─ admin.py
├─ apps.py
├─ models.py
├─ tests.py
├─ views.py
├─__init__.py
│
└─ migrations
       ├─__init__.py
```

上面就是一个 index 应用的结构组成，我们对这些文件做逐一的解释：

*   admin.py 用于将 Model 定义的数据表注册到管理后台，是 Django Admin 应用的配置文件；
*   apps.py 用于应用程序本身的属性配置文件；
*   models.py 用于定义应用中所需要的数据表；
*   tests.py 文件用于编写当前应用程序的单元测试；
*   views.py 用来定义视图处理函数的文件；
*   一级目录下的 __init__.py 文件标识 index 应用是一个 Python 包；
*   migrations 目录用于存储数据库迁移时生成的文件，该目录下的 __init__.py 文件标识 migrations 是一个 Python 包。

这就是 index 应用涉及到的所有文件，当然在实际的开发工作中，该应用目录下的文件也不是一成不变的，开发者根据自己的需要会相应的增加文件或者子目录，比如 urls.py 文件或者存储静态文件的 static 目录等。所以大家千万不要认为 Django 框架自动生成的目录，无需我们做其他操作或者更改，这是使用 Django 的一个误区。

## 2\. 应用的添加

应用创建完成后，我们还需要在 settings.py 配置文件中对其进行添加。在《settings.py 配置文件详解》一文中我们已经对 INSTALLED_APPS 做了介绍，把我们创建的应用添加到这个列表，如下所示：

```

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'index',
]
```

用如上方式添加完成后，就可以让 index 应用的和整个项目融为一体了。

提示：添加应用到 settings.py 中，这个操作看似非常简单，但是初学者却容易忽略或者忘记，所以大家在学习 Django 的过程中一定要注意细节。

本章用了 6 节内容，详细讲解了 Django 项目创建、项目文件配置以及 Django 应用如何创建等，这些属于项目开发前的基础工作。对于一个项目工程而言，基础的项目结构是其精髓，完成了项目结构的搭建，余下的工作就是不断地往里面添加应用功能代码，从而实现具体的业务功能。

通过本章我们可以看出，Django 帮助开发者完成了最为复杂的工作，让开发者只需要关注自己的应用需要提供哪些功能。接下来的章节，就可以正式进入开发应用阶段了，通过实现一些小的功能点，让我们更加全面的了解 Django 框架。