# Python 位运算符的用法

> 原文：[`www.weixueyuan.net/a/368.html`](http://www.weixueyuan.net/a/368.html)

位运算是对数据的二进制位进行操作，这是一种非常底层的操作，但是它的效率很高。

Python 支持常见的位运算，包括按位与、或、异或、取反和移位。本节只讲解与、或、异或和取反操作，将在《Python 移位运算》继续讲解移位运算。

## 1\. 按位与（&）

按位依次进行与操作。该操作需要两个操作数，返回值的每一位的值是两个操作数的对应位进行与操作的结果。该操作并不改变操作数自身，即进行该操作后，两个操作数的值不会发生改变。

与操作真值表如表 1 所示。

表 1：与操作真值表

| A | B | A&B |
| 1 | 1 | 1 |
| 1 | 0 | 0 |
| 0 | 1 | 0 |
| 0 | 0 | 0 |

下面演示了与操作的情况：

```

>>> 1 & 2    # 两个操作数分别是 1 和 2
0            # 高位分别是 0 和 1，所以高位结果为 0
             # 低位分别是 1 和 0，所以低位结果也为 0
             # 最后总的结果为 0
>>> 6 & 12   # 6=0110B，12=1100B
4            # 从高到低，结果依次为 0100B = 4
```

## 2\. 按位或（|）

按位依次进行或操作。该操作需要两个操作数，返回值的每一位的值是两个操作数的对应位进行或操作的结果。该操作并不改变操作数自身，即进行该操作后，两个操作数的值不会发生改变。

或操作真值表如表 2 所示。

表 2：或操作真值表

| A | B | A&#124;B |
| 1 | 1 | 1 |
| 1 | 0 | 1 |
| 0 | 1 | 1 |
| 0 | 0 | 0 |

下面演示了或操作的情况：

```

>>> 1 | 2    # 两个操作数分别是 1 和 2    
3            # 高位分别是 0 和 1，所以高位结果为 1
             # 低位分别是 1 和 0，所以低位结果也为 1
             # 最后总的结果为 11B=3
>>> 6 | 12   # 6=0110B，12=1100B
14           # 从高到低，结果依次为 1110B = 14
```

## 3\. 按位异或（^）

按位依次进行异或操作。该操作也需要两个操作数，返回值的每一位的值是两个操作数的对应位进行异或操作的结果。该操作并不改变操作数自身，即进行该操作后，两个操作数的值并不会发生改变。

异或操作真值表如表 3 所示。

表 3：异或操作真值表

| A | B | A^B |
| 1 | 1 | 0 |
| 1 | 0 | 1 |
| 0 | 1 | 1 |
| 0 | 0 | 0 |

下面演示了异或操作的情况：

```

>>> 1 ^ 2    # 两个操作数分别是 1 和 2    
3            # 高位分别是 0 和 1，所以高位结果为 1
             # 低位分别是 1 和 0，所以低位结果也为 1
             # 最后总的结果为 11B=3
>>> 6 ^ 12   # 6=0110B，12=1100B
10           # 从高到低，结果依次为 1010B = 10
```

## 4\. 按位取反（~）

按位依次进行取反操作。该操作只需要一个操作数，返回值的每一位的值是操作数的对应位进行取反操作的结果。该操作并不改变操作数自身，即进行该操作后，原操作数的值并不会发生改变。

取反操作真值表如表 4 所示。

表 4：取反操作真值表

| A | ~A |
| 1 | 0 |
| 0 | 1 |

下面演示了取反操作的情况：

```

>>> ~100 + 1        # 我们知道一个数的相反数用补码表示的方法是
-100                # 按位取反然后+1
                    # 所以结果就是 100 的相反数为-100
>>> ~(-100) + 1     # 和上面的一样，就是求-100 的相反数
100                 # 所以结果为 100
```

想继续学习 Python 移位操作的读者请猛击《Python 移位运算》。