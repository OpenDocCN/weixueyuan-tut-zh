# Python 移位运算（左移和右移）

> 原文：[`www.weixueyuan.net/a/373.html`](http://www.weixueyuan.net/a/373.html)

移位操作等效于乘以 2 或者除以 2，但是移位运算的效率更高，所以在乘以或者除以 2 的整数幂时使用移位操作可以提升代码的执行速度。

移位操作包括左移和右移两个操作：左移操作相当于得到原操作数除以 2^n，右移操作相当于得到原操作数乘以 2^n。这两个操作都不改变原操作数的值。

## 1\. 左移操作（>>）

相当于得到原操作数除以 2^n，原操作数不发生变化。

```

>>> 2 >> 1   # 得到 2 左移一位的值    
1            # 相当于是 2/2
>>> 2 >> 2   # 得到 2 左移 2 位的值
0            # 相当于先左移一位得到 1，结果 1 再除以 2 等于 0
```

从前面的例子可以看出，每左移一位相当于是除以 2，即 a>>1 等效于 a/2。如果移动 n 位，则等效于连除以 n 个 2，即 a>>n 等效于 (((a>>1)>>1)...)>>1。

如果操作数是正数，那么对之不停进行左移一位操作，其最终结果一定可以得到 0。如果操作数是负数，对之不停进行左移一位操作，是否最后也一定能够得到 0 呢？来看下面的例子：

```

>>> -8>>1   # 移动一位，相当于是除以 2
-4          # 得到-4
>>> -8>>2   # 移动 2 位，相当于是用结果-4 再除以 2
-2
>>> -8>>3   # 移动 3 位，相当于是用结果-2 再除以 2
-1
>>> -8>>4   # 移动 4 位，相当于是用结果-1 再除以 2
-1
```

我们发现，-1 左移一位还是 -1，所以负数不停左移最后会停留在 -1 而不是 0 上。

```

>>> -1>>100   # -1 移动 100 位 
-1            # 依然是-1
```

## 2\. 右移操作（<<）

相当于得到原操作数乘以 2^n，原操作数不发生变化。

```

>>> 3<<1      # 向右移动一位，相当于是乘以 2
6             # 得到 6
>>> -3 << 1   # 向右移动一位，相当于是乘以 2
-6
```