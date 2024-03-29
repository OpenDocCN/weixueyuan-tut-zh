# 1.3 算法和算法分析

算法与数据结构的关系紧密，在算法设计时先要确定相应的数据结构，而在讨论某一种数据结构时也必然会涉及相应的算法。下面就从算法特性、算法描述、算法性能分析与度量等三个方面对算法进行介绍。

## 1.3.1 算法特性

算法（Algorithm）是对特定问题求解步骤的一种描述，是指令的有限序列。其中每一条指令表示一个或多个操作。一个算法应该具有下列特性：

⑴有穷性。一个算法必须在有穷步之后结束，即必须在有限时间内完成。

⑵确定性。算法的每一步必须有确切的定义，无二义性。算法的执行对应着的相同的输入仅有唯一的一条路经。

⑶可行性。算法中的每一步都可以通过已经实现的基本运算的有限次执行得以实现。

⑷输入。一个算法具有零个或多个输入，这些输入取自特定的数据对象集合。

⑸输出。一个算法具有一个或多个输出，这些输出同输入之间存在某种特定的关系。

算法的含义与程序十分相似，但又有区别。一个程序不一定满足有穷性。例如，操作系统，只要整个系统不遭破坏，它将永远不会停止，即使没有作业需要处理，它仍处于动态等待中。因此，操作系统不是一个算法。另一方面，程序中的指令必须是机器可执行的，而算法中的指令则无此限制。算法代表了对问题的解，而程序则是算法在计算机上的特定的实现。一个算法若用程序设计语言来描述，则它就是一个程序。

算法与数据结构是相辅相承的。解决某一特定类型问题的算法可以选定不同的数据结构，而且选择恰当与否直接影响算法的效率。反之，一种数据结构的优劣由各种算法的执行来体现。要设计一个好的算法通常要考虑以下的要求。

⑴正确。算法的执行结果应当满足预先规定的功能和性能要求。

⑵可读。一个算法应当思路清晰、层次分明、简单明了、易读易懂。

⑶健壮。当输入不合法数据时，应能作适当处理，不至引起严重后果。

⑷高效。有效使用存储空间和有较高的时间效率。

## 1.3.2 算法描述

算法可以使用各种不同的方法来描述。

最简单的方法是使用自然语言。用自然语言来描述算法的优点是简单且便于人们对算法的阅读。缺点是不够严谨。

可以使用程序流程图、N-S 图等算法描述工具。其特点是描述过程简洁、明了。用以上两种方法描述的算法不能够直接在计算机上执行，若要将它转换成可执行的程序还有一个编程的问题。可以直接使用某种程序设计语言来描述算法，不过直接使用程序设计语言并不容易，而且不太直观，常常需要借助于注释才能使人看明白。

为了解决理解与执行这两者之间的矛盾，人们常常使用一种称为伪码语言的描述方法来进行算法描述。伪码语言介于高级程序设计语言和自然语言之间，它忽略高级程序设计语言中一些严格的语法规则与描述细节，因此它比程序设计语言更容易描述和被人理解，而比自然语言更接近程序设计语言。它虽然不能直接执行但很容易被转换成高级语言。

## 1.3.3 算法性能分析与度量

我们可以从一个算法的时间复杂度与空间复杂度来评价算法的优劣。当我们将一个算法转换成程序并在计算机上执行时，其运行所需要的时间取决于下列因素：

⑴硬件的速度。例如使用 486 机还是使用 586 机。

⑵书写程序的语言。实现语言的级别越高，其执行效率就越低。

⑶编译程序所生成目标代码的质量。对于代码优化较好的编译程序其所生成的程序质量较高。

⑷问题的规模。例如，求 100 以内的素数与求 1000 以内的素数其执行时间必然是不同的。

显然，在各种因素都不能确定的情况下，很难比较出算法的执行时间。也就是说，使用执行算法的绝对时间来衡量算法的效率是不合适的。为此，可以将上述各种与计算机相关的软、硬件因素都确定下来，这样一个特定算法的运行工作量的大小就只依赖于问题的规模（通常用正整数 n 表示），或者说它是问题规模的函数。

**⒈时间复杂度**

一个程序的时间复杂度（Time complexity）是指程序运行从开始到结束所需要的时间。

一个算法是由控制结构和原操作构成的，其执行时间取决于两者的综合效果。为了便于比较同一问题的不同的算法，通常的做法是：从算法中选取一种对于所研究的问题来说基本运算的原操作，以该原操作重复执行的次数作为算法的时间度量。一般情况下，算法中原操作重复执行的次数是规模 n 的某个函数 T(n)。许多时候要精确地计算 T(n)是困难的，我们引入渐进时间复杂度在数量上估计一个算法的执行时间，也能够达到分析算法的目的。

定义（大Ο记号）：如果存在两个正常数 c 和 n0，使得对所有的 n，n≥n0，有：

f(n) ≤ cg(n)则有：f(n) ＝ Ο(g(n))

例如，一个程序的实际执行时间为 T(n)＝2.7n3+3.8n2+5.3。则 T(n)＝Ο(n3)。使用大Ο记号表示的算法的时间复杂度，称为算法的渐进时间复杂度（Asymptotic Complexity）。

通常用Ο(1)表示常数计算时间。常见的渐进时间复杂度有：

Ο(1)＜Ο(log2n)＜Ο(n)＜Ο(nlog2n)＜Ο(n2)＜Ο(n3)＜Ο(2n)

**⒉空间复杂度**

一个程序的空间复杂度（Space complexity）是指程序运行从开始到结束所需的存储量。

程序的一次运行是针对所求解的问题的某一特定实例而言的。例如，求解排序问题的排序算法的每次执行是对一组特定个数的元素进行排序。对该组元素的排序是排序问题的一个实例。元素个数可视为该实例的特征。程序运行所需的存储空间包括以下两部分：

⑴固定部分。这部分空间与所处理数据的大小和个数无关，或者称与问题的实例的特征无关。主要包括程序代码、常量、简单变量、定长成分的结构变量所占的空间。

⑵可变部分。这部分空间大小与算法在某次执行中处理的特定数据的大小和规模有关。例如 100 个数据元素的排序算法与 1000 个数据元素的排序算法所需的存储空间显然是不同的。