# C++（STL）generate 和 generate_n 函数用法详解

你已经知道可以用 for_each()算法将一个函数对象应用到序列中的每一个元素上。函数对象的参数是 for_each()的前两个参数所指定序列中元素的引用，因此它可以直接修改被保存的值。generate()算法和它有些不同，它的前两个参数是指定范围的正向迭代器，第三个参数是用来定义下面这种形式的函数的函数对象：

T fun (); // T is a type that can be assigned to an element in the range

无法在函数内访问序列元素的值。generate() 算法只会保存函数为序列中每个元素所返回的值，而且 genemte() 没有任何返回值。为了使这个算法更有用，可以将生成的不同的值赋给无参数函数中的不同元素。也可以用一个可以捕获一个或多个外部变量的函数对象作为 generate() 的第三个参数。例如：

```
string chars (30, ' ');// 30 space characters
char ch {'a'};
int incr {};
std::generate (std::begin (chars) , std::end (chars), [ch, &incr]
{
    incr += 3;
    return ch + (incr % 26);})；
std::cout << chars << std: :endl;
// chars is: dgjmpsvybehknqtwzcfiloruxadgjm
```

变量 chars 被初始化为了个有 30 个空格的字符串。作为 generate() 的第三个参数的 lambda 表达式的返回值会被治存到 chars 的连续字符中。lambda 表达式以值的方式捕获 ch，以引用的方式捕获 incr，因此会在 lambda 的主体中对后者进行修改。lambda 表达式会返回 ch 加上 incr 后得到的字符，增加的值是 26 的模，因此返回的值总是在 'a' 到 'z' 之间，给定的起始值为 'a'。这个操作的结果会在注释中展示出来。可以对 lambda 表达式做一些修改， 使它可以用于任何大写或小写字母，但只生成保存在 ch 中的这种类型的字母。

generate_n() 和 generate() 的工作方式是相似的。不同之处是，它的第一个参数仍然是序列的开始迭代器，第二个参数是由第三个参数设置的元素的个数。为了避免程序崩溃，这个序列必须至少有第二个参数定义的元素个数。例如：

```
string chars (30,' '); // 30 space characters
char ch {'a'}/ int incr {};
std::generate_n(std::begin(chars), chars.size()/2,[ch, &incr]
{
    incr += 3;
    return ch + (incr % 26);
})；
```

这里，chars 中只有一半的元素会被算法设为新的值，剩下的一半仍然为空格。