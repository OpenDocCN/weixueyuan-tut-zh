# Makefile 控制函数 error 和 warning

> 原文：[`c.biancheng.net/view/7148.html`](http://c.biancheng.net/view/7148.html)

Makefile 中提供了两个控制 make 运行方式的函数。其作用是当 make 执行过程中检测到某些错误时为用户提供消息，并且可以控制 make 执行过程是否继续。这两个函数是  "error" 和 "warning"，我们来详细的介绍一下这两个函数。

$(error TEXT...)

函数说明如下：

*   函数功能：产生致命错误，并提示 "TEXT..." 信息给用户，并退出 make 的执行。需要说明的是："error" 函数是在函数展开时（函数被调用时）才提示信息并结束 make 进程。因此如果函数出现在命令中或者一个递归的变量定义时，读取 Makefile 时不会出现错误。而只有包含 "error" 函数引用的命令被执行，或者定义中引用此函数的递归变量被展开时，才会提示知名信息 "TEXT..." 同时退出 make。
*   返回值：空
*   函数说明："error" 函数一般不出现在直接展开式的变量定义中，否则在 make 读取 Makefile 时将会提示致命错误。

我们通过两个例子来说明一下；
实例 1：

```

ERROR1=1234
all:
    ifdef ERROR1
    $(error error is $(ERROR1))
    endif     
```

make 读取解析 Makefile 时，如果所起的变量名是已经定义好的"ERROR1"，make 将会提示致命错误信息 "error is 1234" 并保存退出。
实例 2：

```

ERR=$(error found an error!)
.PHONY:err
err:;$(ERR)
```

这个例子，在 make 读取 Makefile 时不会出现致命错误。只有目标 "err" 被作为是一个目标被执行时才会出现。

$(warning TEXT...)

函数说明如下：

*   函数功能：函数 "warning" 类似于函数 "error" ，区别在于它不会导致致命错误（make 不退出），而只是提示 "TEXT..."，make 的执行过程继续。
*   返回值：空
*   函数说明：用法和 "error" 类似，展开过程相同。