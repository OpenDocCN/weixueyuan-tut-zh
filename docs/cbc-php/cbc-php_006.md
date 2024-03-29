# PHP7 有哪些新特性？做了哪些改进？

> 原文：[`c.biancheng.net/view/6109.html`](http://c.biancheng.net/view/6109.html)

PHP 7 是一个具有里程碑意义的版本，它不仅增加了很多新特性，让代码编写更加灵活，还改进了底层设计，提高了 PHP 执行效率。

## PHP 7 新特性

PHP 7 中的新特性主要有以下几点：

*   标量类型声明。
*   函数返回值类型声明。
*   新增 null 合并运算符。
*   新增组合比较符。
*   支持通过 define() 定义常量数组。
*   新增支持匿名类。
*   支持 Unicode codepoint 转译语法。
*   更好的闭包支持。
*   为 unserialize() 提供过滤。
*   新增加 IntlChar 类。
*   支持 use 语句从同一 namespace 导入类、函数和常量。
*   新增整除函数 intdiv()。
*   session_start() 支持接收数组参数。

除了以上列举的 13 点新特性之外，还有其他一些变更，读者可到 [`php.net/manual/zh/migration70.new-features.php`](http://php.net/manual/zh/migration70.new-features.php) 查看有关 PHP 7 新特性的详细变更和示例。

## PHP 7 的改进和优化

相较于以前的版本，PHP 7 在语言语法层面和底层架构层面都有一些改进：

*   在语法层面的改进主要是增加了一些新特性、移除了一些扩展、改变了错误异常处理等。
*   在底层结构方面，改变了存储各种变量的 Zval 和 Zend_String 结构体、优化了 Zend Array 的 HashTable、改进了函数的调用机制等。

尤其是底层结构的改进，大幅提升了 PHP 7 的执行效率，使得其执行速度比 PHP 5 高出一倍左右。

#### PHP 7 引入了严格模式开关

PHP 是一个弱类型的语言，不过在 PHP 7 中支持变量类型的定义，引入了一个开关指令`declare(strict_type=1);`。这个指令一旦开启，就会强制当前文件下的程序遵循严格的函数传参类型和返回类型。不开启 strict_type，PHP 将会尝试转换成要求的类型；开启之后，PHP 不再做类型转换，类型不匹配就会抛出错误。

要使用严格模式，一个 declare 声明指令必须放在文件的顶部。这意味着严格声明标量是基于文件可配的。这个指令不仅影响参数的类型声明，还影响函数的返回值声明。

#### PHP 7 改进了错误处理机制

另外，在 PHP 7 中，很多致命错误以及可恢复的致命错误都被转换为异常来处理了。这些异常继承自 Error 类，此类实现了 Throwable 接口（所有异常都实现了这个基础接口）。

这也意味着，当发生错误的时候，以前代码中的一些错误处理的代码将无法被触发。因为在 PHP 7 版本中，已经使用抛出异常的错误处理机制了。（如果代码中没有捕获 Error 异常，就会引发致命错误）。

#### PHP 7 优化了 Zval

在 2013 年的时候，惠新宸和 Dmitry（PHP 语言内核开发者之一）就曾经在 PHP 5.5 的版本上做过一个 JIT（Just In Time，即时编译，一种软件优化技术）的尝试。

PHP 5.5 原来的执行流程是将 PHP 代码通过词法和语法分析编译成 opcode 字节码，然后 Zend 引擎读取这些 opcode 指令，逐条解析执行。他们在 opcode 环节后又引入了类型推断（TypeInf），然后通过 JIT 生成 ByteCodes 再执行。

采用这种技术优化，PHP 的效率在实际项目中并没有取得明显的提升，于是他们重新设计了 PHP 的底层语言结构。Zval 是存储 PHP 中变量的载体，是一个 C 语言实现的结构体（struct），PHP 5 的 Zval 在内存中占据 24 个字节，而在 PHP 7 中优化后的 Zval 只占 16 个字节，这样变量的存储变得非常简单和高效。

#### PHP 7 优化了数组

PHP 7 优化了数组的 HashTable 实现，PHP 5 的数组存储形式是一个支持双向链表的 HashTable，不仅支持通过数组的 key 来做 hash 映射访问元素，也能通过 foreach 以访问双向链表的方式遍历数组元素。

当我们通过 key 值访问一个元素内容的时候，有时需要 3 次的指针跳跃才能找对需要的内容。最重要的一点是，这些数组元素的存储是分散在各个不同的内存区域的，在 CPU 读取的时候，因为它们很可能不在同一级缓存中，导致 CPU 不得不到下级缓存甚至内存区域查找，从而引起 CPU 缓存命中下降，进而增加更多的耗时。

优化后的 Zend Array 最大的特点是整块的数组元素和 hash 映射表全部连接在一起，被分配在同一块内存中。如果是遍历一个整型的简单类型数组，效率会非常快，因为数组元素（Bucket）本身是连续分配在同一块内存里的，并且数组元素的 Zval 会把整型元素存储在内部，也不再有指针外链，全部数据都存储在当前内存区域内。

当然，最重要的是它能够避免 CPU 缓存命中率下降。

#### PHP 7 改进了函数调用

PHP 7 还改进了函数的调用机制，通过优化参数传递的环节减少了一些指令，提高执行效率。