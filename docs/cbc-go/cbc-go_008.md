# Go 语言代码风格清晰、简单

Go 语言写起来类似于 C 语言，因此熟悉 C 语言及其派生语言（C++、C#、Objective-C 等）的人都会迅速熟悉这门语言。

C 语言的有些语法会让代码可读性降低甚至发生歧义。Go 语言在 C 语言的基础上取其精华，弃其糟粕，将 C 语言中较为容易发生错误的写法进行调整，做出相应的编译提示。

## 1) 去掉循环冗余括号

Go 语言在众多大师的丰富实战经验的基础上诞生，去除了 C 语言语法中一些冗余、烦琐的部分。下面的代码是 C 语言的数值循环：

```
// C 语言的 for 数值循环
for(int a = 0;a<10;a++){
    // 循环代码
}
```

在 Go 语言中，这样的循环变为：

```
for a := 0;a<10;a++{
    // 循环代码
}
```

for 两边的括号被去掉，int 声明被简化为`:=`，直接通过编译器右值推导获得 a 的变量类型并声明。

## 2) 去掉表达式冗余括号

同样的简化也可以在判断语句中体现出来，以下是 C 语言的判断语句：

```
if (表达式){
    // 表达式成立
}
```

在 Go 语言中，无须添加表达式括号，代码如下：

```
if 表达式{
    // 表达式成立
}
```

## 3) 强制的代码风格

Go 语言中，左括号必须紧接着语句不换行。其他样式的括号将被视为代码编译错误。这个特性刚开始会使开发者有一些不习惯，但随着对 Go 语言的不断熟悉，开发者就会发现风格统一让大家在阅读代码时把注意力集中到了解决问题上，而不是代码风格上。

同时 Go 语言也提供了一套格式化工具。一些 Go 语言的开发环境或者编辑器在保存时，都会使用格式化工具进行修改代码的格式化，让代码提交时已经是统一格式的代码。

## 4) 不再纠结于 i++ 和 ++i

C 语言非常经典的考试题为：

```
int a, b;
a = i++;
b = ++i;
```

这种题目对于初学者简直摸不着头脑。为什么一个简单的自增表达式需要有两种写法？

在 Go 语言中，自增操作符不再是一个操作符，而是一个语句。因此，在 Go 语言中自增只有一种写法：

i++

如果写成前置自增`++i`，或者赋值后自增`a=i++`都将导致编译错误。