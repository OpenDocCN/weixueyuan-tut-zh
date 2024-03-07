# Python 求平方根（附带源码）

> 原文：[`www.weixueyuan.net/a/388.html`](http://www.weixueyuan.net/a/388.html)

对于求一个浮点数的平方根，可以使用库函数，如 math.sqrt()。但在本节，为了演示浮点数的一些用法，我们用下面的迭代法来求某个数的平方根。

其基本原理如下，对于一个浮点数 a，其平方根一定在 1 和 a 之间：

*   如 a=2，那么其平方根 1.414 在 1 和 2 之间。
*   如 a=0.8，那么其平方根 0.89443 在 0.8 和 1 之间。

假定 a>1，我们的方法是取 1 和 a 的中间值 a1=(a+1)/2，如果 a1 的平方大于 a，那么平方根一定在 1 和 a1 之间；否则在 a1 和 a 之间。依次迭代直到误差足够小。

下面是实现代码：

```

import sys  # 用来获得用户输入参数
def cal_sqrt_with_newton(start, end, target, stop):  # 计算平方根
    while True:  # 一直循环
        mid = (start + end) / 2.0
        if abs((mid * mid) - target) < stop:  # 如果精度达到要求了
            return mid  # 返回中间值
        else:
            if (mid * mid) > target:  # 否则继续二分法
                end = mid
            else:
                start = mid
def py_sqrt(v):
    if v > 1.0:  # 如果值大于 1，那么在 1 和 v 之间
        return cal_sqrt_with_newton(1.0, v, v, 1.0e-6)
    else:  # 如果值小于 1，那么在 v 和 1 之间
        return cal_sqrt_with_newton(v, 1.0, v, 1.0e-6)
def test(v):  # 测试计算结果
    ret = py_sqrt(v)
    print("sqrt(%s) = %s" % (v, ret))  # 显示计算结果
if __name__=='__main__':
    input = float(sys.argv[1])
    test(input)
```

运行后的结果如下：

```

$ python sqrt2.py 2.0       # 计算 2 的平方根
sqrt(2.0) = 1.41421365738   # 计算结果
$ python sqrt2.py 0.5       # 计算 0.5 的平方根
sqrt(0.5) = 0.707106590271  # 计算结果
```

需要注意的是，由于使用了递归调用，如果希望得到高精度的结果，就有可能出现调用深度超出。