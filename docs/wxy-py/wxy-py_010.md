# Python 注释的用法

> 原文：[`www.weixueyuan.net/a/363.html`](http://www.weixueyuan.net/a/363.html)

注释是不会被执行的。Python 使用类似于 Shell 的行注释方式，以`#`开始一直到行结束，这些内容都被认为是注释。解释器是不会对这部分内容进行任何处理的。

另外一种特殊的注释方法，或者严格来说不叫注释，叫作字符串注释。就是将注释内容放入模块、函数、类的起始字符串，该字符串也被称作“文档字符串(docstring)”。

下面的代码中，有两处是用“#”开始的代码，有一处是用字符串进行注释的，我将它们都标注出来了。

```

#计算最大值（单行注释）
def max(input_list):
    """计算输入列表中元素的最大值，如果
    输入的不是列表，则返回 None
    （字符串注释）
    """
    if isinstance(input_list, list):
        ret = input_list[0]
        for x in input_list:
            if x > ret:
                ret = x
        return ret
    else:
        #如果输入的不是列表，那么返回 None（单行注释）
        return None

if __name__ == "__main__":
    ret = max([1, 3, 5, 0, 11])
    print(u"最大值为%d" % ret)
```

运行结果：
11