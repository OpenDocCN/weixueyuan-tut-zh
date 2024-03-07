# C++优先级队列存储智能指针详解

现在主要讲解智能指针的使用。这和原生指针在本质上是相同的，除非想要自己负责删除它们所指向的对象。当生成优先级队列或堆时，需要一个顺序关系来确定元素的顺序。当它们保存的是原生指针或智能指针时，总是需要为它们提供一个比较函数；如果不提供，就会对它们所保存的指针而不是指针所指向的对象进行比较，这肯定不是我们所希望的。

让我们考虑一下，如何定义一个保存指针的 priority_queue，指针则指向自由存储区的 string 对象。为了缩短代码中语句的长度，假定下面的 using 声明对本节随后的内容都生效：

```
using std::string;
using std::shared_ptr;
using std::unique_ptr;
```

我们需要定义一个用来比较对象的函数对象，被比较的对象由 shared_ptr<string> 类型的指针指向。按如下所示定义比较函数：

```
auto comp = [] (const shared_ptr<string>& wp1, const shared_ptr<string>& wp2)
{ return *wp1 < *wp2; };
```

这里定义了一个 lambda 表达式 comp，可以用来比较两个智能指针所指向的对象。对 lambda 表达式命名的原因是需要将它作为 priority_queue 模板的类型参数。下面是优先级队列的定义：

```
std::priority_queue<shared_ptr<string>,std::vector<shared_ptr<string>>, decltype(comp)>wordsl {comp};
```

第一个模板类型参数是所保存元素的类型，第二个用来保存元素的容器的类型，第三个是用来比较元素的函数对象的类型。我们必须指定第三个模板类型参数，因为 lambda 表达式的类型和默认比较类型 std::less<T> 不同。

仍然可以指定一个外部容器来初始化存放指针的 priority_queue:

```
std::vector<shared_ptr<string>>init { std::make—shared<string> ("one"),std::make_shared<string>("two"), std::make_shared<string>("three"),std::make_shared<string> ("four") };
std::priority_queue<shared_ptr<string>, std::vector<shared_ptr<string>>, decltype(comp)>words(comp, init);
```

vector 容器 init 是用 make_s!iared<string>() 生成的初始值。优先级队列构造函数的参数是一个定义了元素比较方式的对象以及一个提供初始化元素的容器。先复制 vector 中的智能指针，然后用它们来初始化优先级队列 words。当然，也可以用另一个容器中的元素来初始化优先级队列，这意味不能使用 unique_ptr<string> 元素，必须是 shared_ptr<string>。

如果初始元素不需要保留，可以用 priority_queue 对象的成员函数 emplace() 直接在容器中生成它们：

```
std::priority_queue<shared_ptr<string>,std::vector<shared_ptr<string>>, decltype(comp)>words1{comp};

words1.emplace(new string {"one"});
words1.emplace(new string {"two”});
words1.emplace(new string {"three"});
words1.emplace(new string {"five"});
```

words1 的成员函数 emplace() 会调用它们所保存对象的类型的构造函数，也就是 shared_ ptr<string> 的构造函数。这个构造函数的参数是自由存储区的一个 string 对象的地址， string 对象是由 emplace() 的参数表达式生成的。这段代码会在优先级队列中保存 4 个 string 对象的指针，它们分别指向"two"、"three"、"one"、"five"这 4 个对象。元素在优先级队列中的顺序取决于这一节前面定义的 comp。

当然，如果不需要保留初始元素，可以用优先级队列保存 unique_ptr<string> 元素。例如：

```
auto ucomp = [](const std::unique_ptr<string>& wp1, const std::unique_ptr<string>& wp2){ return *wp1 < *wp2; };
std::priority_queue<std::unique_ptr<string>, std::vector<std::unique_ptr<string»,decltype(ucomp)> words2 {ucomp};
```

这个定义比较运算的 lambda 表达式可以接受 unique_ptr<string> 对象的引用。我们需要为这个优先级队列指定 3 个模板类型参数，因为我们需要指定比较函数的类型。第二个模板类型参数可以是 deque<String>，它是默认使用的容器类型。也可以用 emplace() 向优先级队列中添加元素：

```
words2.emplace(new string{"one"});
words2.emplace(new string {"two"});
words2.emplace (new string {"three"});
words2.emplace(new string {"five"});
```

或者，也可以使用 push():

```
words2.push(std::make_unique<string>("one"));
words2.push(std::make_unique<string>("two"));
words2.push(std::make_unique<string>("three"));
words2.push(std::make_unique<string>("five"));
```

make_imique<string>() 返回的对象会被移到容器中，这是由右值引用参数版的 push() 自动选择的。