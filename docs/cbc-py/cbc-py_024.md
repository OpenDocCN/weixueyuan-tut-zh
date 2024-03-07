# Python 三目运算符（三元运算符）用法详解

Python 可通过 if 语句来实现三目运算符的功能，因此可以近似地把这种 if 语句当成三目运算符。作为三目运算符的 if 语句的语法格式如下：

True_statements if expression else False_statements

三目运算符的规则是：先对逻辑表达式 expression 求值，如果逻辑表达式返回 True，则执行并返回 True_statements 的值；如果逻辑表达式返回 False，则执行并返回 False_statements 的值。看如下代码：

```
a = 5
b = 3
st = "a 大于 b" if a > b else  "a 不大于 b"
# 输出"a 大于 b"
print(st)
```

实际上，如果只是为了在控制台输出提示信息，还可以将上面的三目运算符表达式改为如下形式：

```
# 输出"a 大于 b"
print("a 大于 b") if a > b else print("a 不大于 b")
```

Python 允许在三目运算符的 True_statements 或 False_statements 中放置多条语句。Python 主要支持以下两种放置方式：

1.  多条语句以英文逗号隔开：每条语句都会执行，程序返回多条语句的返回值组成的元组。
2.  多条语句以英文分号隔开：每条语句都会执行，程序只返回第一条语句的返回值。

先看第一种情形，使用如下代码：

```
# 第一个返回值部分使用两条语句，逗号隔开
st = print("crazyit"), 'a 大于 b' if a > b else  "a 不大于 b"
print(st)
```

上面程序中 True_statements 为 print("crazyit")，'a 大于 b'，这两条语句都会执行，程序将会返回这两条语句的返回值组成的元组。由于 print() 函数没有返回值，相当于它的返回值是 None。运行上面代码，将看到如下结果：

crazyit
(None,'a 大于 b')

如果将上面语句中的逗号改为分号，将逗号之后的语句改为赋值语句，即写成如下形式：

```
# 第一个返回值部分使用两条语句，分号隔开
st = print("crazyit"); x = 20 if a > b else  "a 不大于 b"
print(st)
print(x)
```

此时虽然 True_statements 包含两条语句，但程序只会返回第一条语句 print(”crazyit”) 的返回值，该语句同样返回 None，因此相当于 str 的返回值为 None。运行上面代码，将看到如下结果：

crazyit
None
20

需要指出的是，三目运算符支持嵌套，通过嵌套三目运算符，可以执行更复杂的判断。例如，下面代码需要判断 c、d 两个变量的大小关系：

```
c = 5
d = 5
# 下面将输出 c 等于 d
print("c 大于 d") if c > d else (print("c 小于 d") if c < d else print("c 等于 d"))
```

上面代码首先对 c>d 求值，如果该表达式为 True，程序将会执行并返回第一个表达式：print(”c 大于 d”)；否则系统将会计算 else 后面的内容：(print("c 小于 d") if c < d else print("c 等于 d"))，这个表达式又是一个嵌套的三目运算符表达式。注意，进入该表达式时只剩下“c 小于 d”或“c 等于 d”两种情况，因此该三目运算符再次判断 c<d，如果该表达式为 True，将会输出“c 小于 d”；否则只剩下“c 等于 d”一种情况，自然就输出该字符串了。