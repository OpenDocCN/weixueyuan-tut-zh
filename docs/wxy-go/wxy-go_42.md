# Go 语言闭包

> 原文：[`www.weixueyuan.net/a/535.html`](http://www.weixueyuan.net/a/535.html)

闭包并不是什么新奇的概念，它早在高级语言开始发展的年代就产生了。

## 1、闭包的概念

闭包（Closure）是词法闭包（Lexical Closure）的简称。是由函数和与其相关的引用环境组合而成的实体。在实现深约束时，需要创建一个能显式表示引用环境的东西，并将它与相关的子程序捆绑在一起，这样捆绑起来的整体被称为闭包。函数 + 引用环境 = 闭包。

闭包只是在形式和表现上像函数，但实际上不是函数。函数是一些可执行的代码，这些代码在函数被定义后就确定了，不会在执行时发生变化，所以一个函数只有一个实例。

闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。闭包在某些编程语言中被称为 Lambda 表达式。

函数本身不存储任何信息，只有与引用环境结合后形成的闭包才具有“记忆性”。函数是编译器静态的概念，而闭包是运行期动态的概念。

对象是附有行为的数据，而闭包是附有数据的行为。

## 2、闭包的优点

#### 1) 加强模块化

闭包有益于模块化编程，便于以简单的方式开发较小的模块，从而提高开发速度和程序的可复用性。和没有使用闭包的程序相比，使用闭包可将模块划分得更小。

比如要计算一个数组中所有数字的和，只需要循环遍历数组，把遍历到的数字加起来就行了。如果现在要计算所有元素的积，又或者要打印所有的元素呢？解决这些问题都要对数组进行遍历，如果是在不支持闭包的语言中，程序员不得不一次又一次重复地写循环语句。而这在支持闭包的语言中是不必要的。这种处理方法多少有点像回调函数，不过要比回调函数写法更简单，功能更强大。

#### 2) 抽象

闭包是数据和行为的组合，这使得闭包具有较好的抽象能力。

#### 3) 简化代码

一个编程语言需要以下特性来支持闭包。

*   函数是一阶值（First-class value，一等公民），即函数可以作为另一个函数的返回值或参数，还可以作为一个变量的值；
*   函数可以嵌套定义，即在一个函数内部可以定义另一个函数；
*   允许定义匿名函数；
*   可以捕获引用环境，并把引用环境和函数代码组成一个可调用的实体。

接下来通过三个示例的对比来更清晰地了解闭包的作用。

【示例】没有使用闭包进行计数的代码。

```

package main
import "fmt"
func main() {
    for i := 0; i < 5; i++ {
        fmt.Printf("i=%d \t", i)
        fmt.Println(add2(i))
    }
}
func add2(x int) int {
    sum := 0
    sum += x
    return sum
}
```

运行结果如下：

i=0     0
i=1     1
i=2     2
i=3     3
i=4     4

由上面的示例可以看出，for 循环每执行一次，sum 都会清零，没有实现 sum 累加计数。

【示例】使用闭包函数实现计数器。

```

package main
import "fmt"
func main() {
    pos := adder()
    for i := 0; i < 10; i++ {
        fmt.Printf("i=%d \t", i)
        fmt.Println(pos(i))
    }
    fmt.Println("--------------------")
    for i := 0; i < 10; i++ {
        fmt.Printf("i=%d \t", i)
        fmt.Println(pos(i))
    }
}
func adder() func(int) int {
    sum := 0
    return func(x int) int {
        fmt.Printf("sum1=%d \t", sum)
        sum += x
        fmt.Printf("sum2=%d \t", sum)
        return sum
    }
}
```

运行结果如下：

i=0     sum1=0  sum2=0  0
i=1     sum1=0  sum2=1  1
i=2     sum1=1  sum2=3  3
i=3     sum1=3  sum2=6  6
i=4     sum1=6  sum2=10     10
i=5     sum1=10     sum2=15     15
i=6     sum1=15     sum2=21     21
i=7     sum1=21     sum2=28     28
i=8     sum1=28     sum2=36     36
i=9     sum1=36     sum2=45     45
--------------------
i=0     sum1=45     sum2=45     45
i=1     sum1=45     sum2=46     46
i=2     sum1=46     sum2=48     48
i=3     sum1=48     sum2=51     51
i=4     sum1=51     sum2=55     55
i=5     sum1=55     sum2=60     60
i=6     sum1=60     sum2=66     66
i=7     sum1=66     sum2=73     73
i=8     sum1=73     sum2=81     81
i=9     sum1=81     sum2=90     90

【示例】闭包使用案例。

```

package main
import "fmt"
func main() {
    myfunc := Counter()
    // fmt.Printf("%T\n", myfunc)
    fmt.Println("myfunc", myfunc)
    // 调用 myfunc 函数，i 变量自增 1 并返回
    fmt.Println(myfunc())
    fmt.Println(myfunc())
    fmt.Println(myfunc())
    // 创建新的函数 nextNumber1，并查看结果
    myfunc1 := Counter()
    fmt.Println("myfunc1", myfunc1)
    fmt.Println(myfunc1())
    fmt.Println(myfunc1())
}
//计数器，闭包函数
func Counter() func() int {
    i := 0
    res := func() int {
        i += 1
        return i
    }
    // fmt.Printf("%T, %v \n", res, res)
    fmt.Println("Counter 中的内部函数：", res)
    return res
}
```

运行结果如下：

Counter 中的内部函数： 0x49ac50
myfunc 0x49ac50
1
2
3
Counter 中的内部函数： 0x49ac50
myfunc1 0x49ac50
1
2

由于闭包函数“捕获”了和它在同一作用域的其他常量和变量，所以当闭包在任何地方被调用，闭包都可以使用这些常量或者变量。它不关心这些变量是否已经超出作用域，只要闭包还在使用这些变量，这些变量就依然存在。