# C++ find_first_of(STL find_first_of)查找算法详解

find_first_of() 算法可以在第一个序列中搜索第二个序列在第一个序列中第一次出现的任何元序列被搜索的范围由输入迭代器指定，但用于确定搜索序列的迭代器至少是正向迭代器。用 == 运算符来比较这两个序列中的元素，所以如果序列中是类类型的对象，这个类必须实现 operator==()。下面是一个使用 find_first_of() 的示例：

```
string text {"The world of searching"};
string vowels {"aeiou"};
auto iter = std::find_first_of (std::begin (text), std:: end (text), std::begin (vowels),std::end(vowels));
if(iter != std::end(text))
    std::cout << "We found '" << *iter << "'." << std::endl; //We found 'e'.
```

这段代码会在 text 中查找第一次出现的 vowels 中的任意字符。在这个示例中，返回的迭代器指向“The”的第三个字母。可以用循环来查找 text 中所有匹配 vowels 中字符的字符：

```
string found {}; // Records characters that are found
for(auto iter = std::begin(text);(iter = std::find_first_of (iter,std::end (text),std::begin(vowels),std::end(vowels)))!= std::end(text);)
        found += *(iter++);
std::cout << "The characters \"" << found << "\" were found in text." << std::endl;
```

为了展示我们可以这样做，这里使用了 for 循环。第一个循环控制表达式以 text 的开始迭代器为初值定义了 iter。第二个循环控制表达式调用 find_first_of()，在 [iter,std::end(text))  这段范围内搜索第一次出现的 vowel 中的字符。

find_first_of() 返回的迭代器保存在 iter 中，然后它被用来和 text 的结束迭代器进行比较。如果 iter 现在是 text 的结束迭代器，循环结束。如果 iter 不是 text 的结束迭代器，循环体会将这个字符附加到 iter 指向的 found 字符串上，然后自增 iter，使它指向下一个字符。这个字符会作为下一次搜索的开始位置。这段代码产生的输出为：

The characters "eooeai" were found in text.

另一个版本的 find_first_of() 可以让我们在第二个序列中搜索指定范围内的，可以使第 5 个参数指定的二元谓词返回 true 的元素。这个序列中的元素不必是同种类型。当这个 == 运算符不支持所有这些元素的比较时，就可以用这个版本的算法来定义相等比较，但也可以用其他的方式来定义。例如：

```
std::vector<long> numbers{64L, 46L, -65L, -128L, 121L, 17L, 35L, 9L, 91L, 5L};
int factors[] {7, 11, 13};
auto iter = std::find_first_of(std::begin(numbers),std::end(numbers),std::begin(factors), std::end(factors),[](long v, long d) { return v % d == 0; });// Predicate - true for a match
if (iter != std::end (numbers))
    std::cout << *iter << " was found." << std::endl;
```

这个谓词是一个 lambda 表达式，当第一个参数可以被第二个参数整除时，它返回 true。

所以这段代码会找到 -65，因为这是 numbers 中第一个可以被 factors 数组中的元素 13 整除的元素。断言中的参数类型可以和序列元素的类型不同，只要每个序列中的元素可以隐式转换为参数所对应的类型。在这里，factors 中的元素可以隐式转换为 long 类型。

当然，可以用循环来查找所有使谓词返回 true 的元素：

```
std::vector<long> numbers {64L, 46L, -65LA -128L, 121L, 17L, 35L, 9L, 91L, 5L};
int factors[] {7, 11, 13};
std::vector<long> results;  // Stores elements found
auto iter = std::begin (numbers);
while ((iter = std:: find_first_of (iter, std::end (numbers),std::begin(factors), std::end(factors),[](long vr long d) { return v % d == 0; }))!= std::end(numbers))
    results.push_back(*iter++);
std::cout << results.size() <<" values were found:\n";
std::copy(std::begin(results), std::end(results),std::ostream_iterator < long > {std::cout," " });
std::cout << std::endl;
```

这段代码可以找出 numbers 中所有以 factors 中的一个元素为因子的元素。while 循环会持续进行，只要 find_first_of() 返回的迭代器不是 numbers 的结束迭代器。iter 变量开始时指向 numbers 的第一个元素，然后会用它来保存被找到元素的迭代器，先前的值被覆盖。在循环体中，iter 指向的元素会被保存到 result 容器中，然后 iter 自增指向下一个元素。当循环结束时，results 包含的是找到的所有元素，然后用 copy() 算法输出它们。