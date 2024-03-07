# Django CBV 基类 View 源码解析

> 原文：[`c.biancheng.net/view/7833.html`](http://c.biancheng.net/view/7833.html)

前述章节《Django 的 FBV 与 CBV 模式》中我们讲解了 Django 中编写视图层函数的两种方式，一种是基于函数即 FBV，另外一种是 CBV 即基于类的视图函数。在本节，我们对类视图中所继承的 View 源码进一步分析，帮助大家更好的理解类视图。若以后在项目中使用它就会更加得心应手。

View 定义于 django/views/generic/base.py 文件中，其功能实现主要依赖于三个重要的方法分别如下所示：

*   dispatch
*   as_view
*   http_method_not_allowed

下面我们根据源码依次对这个三个方法进行分析。

## 1\. http_method_not_allowed 方法

这个方法返回 HttpResponseNotAllowed（405）响应，属于继承 HttpResponse 的子类，表示当前的请求类型不被支持。它在 View 类中的源码如下所示：

```

class View:
    def http_method_not_allowed(self, request, *args, **kwargs):
        logger.warning(
            'Method Not Allowed (%s): %s', request.method, request.path,
            extra={'status_code': 405, 'request': request}
        )
        return HttpResponseNotAllowed(self._allowed_methods())
```

比如当一个类视图函数之定义了 get 方法，而没有定义 post 方法，那么这个类视图函数接收到 post 的请求的时候，由于找不到相对应的 post 的定义，就会通过另一个方法 dispatch 分发到 http_method_not_allowed 方法中。

## 2\. dispatch 方法分析

dispatch 方法根据 HTTP 请求类型调用 View 中的同名函数，实现了请求的分发，源码如下所示：

```

class View： 
  def dispatch(self, request, *args, **kwargs):
        if request.method.lower() in self.http_method_names:
            handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
        else:
            handler = self.http_method_not_allowed
        return handler(request, *args, **kwargs)
```

http_method_names 定义当前 View 可以接受的请求类型：

```

http_method_names = ['get', 'post', 'put', 'patch', 'delete', 'head', 'options', 'trace']
```

首先，if 判断当前的请求类型是否可以被接受，即请求方是否定义在 http_method_names 变量中。若能够接受请求，则 dispatch() 尝试获取 View 中的同名方法，如果不存在，则将 handler 指定为 http_method_not_allowed 即不被允许的方法。然后 else 规定如果方法不被接受则直接将其指定为 http_method_not_allowed。

## 3\. as_view 方法分析

 Django 给 as_view 方法加了`@classonlymethod`装饰器，作用是只允许类对象调用这个方法，如果是类实例调用，将会抛出 AttributeError 异常。该方法的源码如下所示：

```

class View:   
@classonlymethod
    def as_view(cls, **initkwargs):
        for key in initkwargs:
            if key in cls.http_method_names:
                raise TypeError("You tried to pass in the %s method name as a "
                                "keyword argument to %s(). Don't do that."
                                % (key, cls.__name__))
            if not hasattr(cls, key):
                raise TypeError("%s() received an invalid keyword %r. as_view "
                                "only accepts arguments that are already "
                                "attributes of the class." % (cls.__name__, key))

        def view(request, *args, **kwargs):
            #创建 View 类实例
            self = cls(**initkwargs)
            if hasattr(self, 'get') and not hasattr(self, 'head'):
                self.head = self.get
            self.setup(request, *args, **kwargs)
            if not hasattr(self, 'request'):
                raise AttributeError(
                    "%s instance has no 'request' attribute. Did you override "
                    "setup() and forget to call super()?" % cls.__name__
                )
            #调用 View 实例的 dispatch 方法
            return self.dispatch(request, *args, **kwargs)
        view.view_class = cls
        view.view_initkwargs = initkwargs
        update_wrapper(view, cls, updated=())
        update_wrapper(view, cls.dispatch, assigned=())
        return view
```

Django 将一个 HTTP 请求映射到一个可调用的函数，而不是一个类对象。所以，在定义 URL 路由的时候总是需要调用 View 的 as_view 方法。可以看到，上述的 as_view 方法，它创建了 View 类的实例，然后调用了 dispatch 方法，根据请求类型分发处理请求函数。

在本节我们通过介绍 View 的源码，详细解析了基于类视图的实现原理，当一个请求进来的时候，视图层是如何让处理的，分别调用了哪些方法，最终实现了请求的分发处理。