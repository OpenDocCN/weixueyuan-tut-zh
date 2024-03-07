# C 语言基本运算符

我们小学数学学过加、减、乘、除等运算符号以及四则混合运算，而这些运算符号在 C 语言中也有，但是有些表达方法不一样，并且还有额外的运算符号。在 C 语言编程中，加、减、乘、除和取余数的符号分别是：+、-、*、/、%。此外，C 语言中还有额外的两个运算符++和--，他们的用法是一样的，一个是自加 1，一个是自减 1，我们选++来讲一下。

++在用法上就是加 1 的意思，注意是变量自己加，比如 b++的意思就是 b=b+1，而在编程的时候我们有两种常用的方式先加和后加。比如 unsigned char a = 0; unsigned char b = 0;那么 a = ++b;的整个运算过程是先计算 b=b+1，那么 b 就等于 1 了，然后再运行 a=b，运行完毕后 a=1，b=1。如果写成 a=b++;那么运算过程就是先执行 a=b;然后再执行 b=b+1，执行完的结果就是 a=0，b=1。

刚刚讲的叫做算数运算符，但是其中用到了 C 语言一个很重要的赋值运算符“=”。我们前边的程序在不停的用，但是始终没有详细诠释这个运算符。在 C 语言里，“=”代表的意思是赋值，而不是等于。最经典的一个例子就是 a=1;b=2;如果写成 a = a+b;这个在数学里的运算是 a 等于 a 加 b，但是在 c 语言里的意思是把 a 加 b 的结果送给 a，那么运算完了之后的结果是 a 等于 3，b 还等于 2。

说到这里就不得不说 C 语言的比较运算符“==”。这个在 C 语言里是进行是否等于判断的关系运算符，而“!=”就是不等于的关系运算符。这些运算符这里就是简单介绍一下，而后边我们会通过使用来帮助大家巩固这些知识。其他一些运算符，在使用过程中我们也会陆陆续续介绍到。