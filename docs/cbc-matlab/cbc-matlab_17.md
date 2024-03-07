# MATLAB 变量和关键字

> 原文：[`c.biancheng.net/view/6629.html`](http://c.biancheng.net/view/6629.html)

变量和关键字是 MATLAB 编程中最基本的两个概念，它们是构成 MATLAB 表达式的常见元素。

## MATLAB 变量

变量是在程序运行中值可以改变的量，变量由变量名来表示。

在 MATLAB 语言中变量的命名遵循如下规则：

*   变量名区分大小写。
*   变量名长度不得超过 31 位，第 31 个字符之后的字符将被 MATLAB 语言所忽略。
*   变量名以字母开头，可以是字母、数字、下划线组成，但不能使用标点。

MATLAB 不要求事先对变量进行声明，也不需要指定变量类型，MATLAB 会自动依据所赋予变量的值或对变量所进行的操作来识别变量的类型。

在赋值过程中如果如果赋值变量已存在时，MATLAB 将使用新值代替旧值，并以新值类型代替旧值类型。

在 MATLAB 语言中也存在变量作用域的问题。在未加特殊说明的情况下，MATLAB 语言将所识别的一切变量视为局部变量，即仅在其使用的 M 文件内有效。

若要将变量定义为全局变量，则应当对变量进行说明，即在该变量前加关键字 global。一般来说，全局变量均用大写的英文字符表示。

在 MATLAB 中，有一类特殊的变量，是由系统默认给定符号来表示的。例如 pi，它代表圆周率`π`这个常数，即 3.1415926... 这类变量类似于 C 语言中的符号常量，有时又称为系统预定义的变量。

下表给出了一些常用的特殊变量及其含义。

特殊变量名表

| 变量名称 | 变量含义 | 变量名称 | 变量含义 |
| ans | MATLAB 中的默认变量 | i(j) | 复数中的虚数单位 |
| pi | 圆周率 | nargin | 所用函数的输入变量数 |
| eps | 计算机中的最小数，PC 机上为 2 的负 52 次方 | nargout | 所用函数的输出变量数 |
| inf | 无穷大，如 1/0 | realmin | 最小可用正实数 |
| NaN | 不定值，如 0/0 等 | realmax | 最大可用正实数 |

## MATLAB 关键字

MATLAB 的关键字是指预先定义的有特别意义的标识符，有时也叫保留字。

像其他语言一样，MATLAB 包含算数运算、逻辑运算、关系运算等运算类型，每种类型的运算都有自己的运算符，并可对关系和逻辑进行运算，而且都有相应的运算函数。

这些有特定意义的符号和名字是不能被指定为变量名称的。比如下表中的特殊变量名、MATLAB 的管理类命令、编程控制语句等。

与其他语言相比，MATLAB 的强大主要体现在它提供了各种类别的函数。

除了基本的数学函数之外，MathWorks 公司针对不同领域，推出了信号处理、控制系统、神经网络、图像处理、小波分析、鲁棒控制、非线性系统控制设计、系统辨识、优化设计、统计分析、财政金融、样条、通信等 30 多个具有专门功能的工具箱，这些工具箱里面的函数是由各个领域内学术水平较高的专家编写的，无须用户自己编写所用的专业基础程序，可直接对工具箱进行运用。

同时，工具箱内的函数源程序也是开放的，多为 M 文件，用户可以查看这些文件的代码并进行更改，MATLAB 支持用户对其函数进行二次开发，用户的应用程序也可以作为新函数添加到相应的工具箱中。