# C++ decltype 类型推导完全攻略

> 原文：[`c.biancheng.net/view/7151.html`](http://c.biancheng.net/view/7151.html)

decltype 是 C++11 新增的一个关键字，它和 auto 的功能一样，都用来在编译时期进行自动类型推导。不了解 auto 用法的读者请转到《C++ auto》。

decltype 是“declare type”的缩写，译为“声明类型”。

既然已经有了 auto 关键字，为什么还需要 decltype 关键字呢？因为 auto 并不适用于所有的自动类型推导场景，在某些特殊情况下 auto 用起来非常不方便，甚至压根无法使用，所以 decltype 关键字也被引入到 C++11 中。

auto 和 decltype 关键字都可以自动推导出变量的类型，但它们的用法是有区别的：

auto varname = value;
decltype(exp) varname = value;

其中，varname 表示变量名，value 表示赋给变量的值，exp 表示一个表达式。

auto 根据`=`右边的初始值 value 推导出变量的类型，而 decltype 根据 exp 表达式推导出变量的类型，跟`=`右边的 value 没有关系。

另外，auto 要求变量必须初始化，而 decltype 不要求。这很容易理解，auto 是根据变量的初始值来推导出变量类型的，如果不初始化，变量的类型也就无法推导了。decltype 可以写成下面的形式：

decltype(exp) varname;

#### exp 注意事项

原则上讲，exp 就是一个普通的表达式，它可以是任意复杂的形式，但是我们必须要保证 exp 的结果是有类型的，不能是 void；例如，当 exp 调用一个返回值类型为 void 的函数时，exp 的结果也是 void 类型，此时就会导致编译错误。

C++ decltype 用法举例：

```

int a = 0;
decltype(a) b = 1;  //b 被推导成了 int
decltype(10.8) x = 5.5;  //x 被推导成了 double
decltype(x + 100) y;  //y 被推导成了 double
```

可以看到，decltype 能够根据变量、字面量、带有运算符的表达式推导出变量的类型。读者请留意第 4 行，y 没有被初始化。

## decltype 推导规则

上面的例子让我们初步感受了一下 decltype 的用法，但你不要认为 decltype 就这么简单，它的玩法实际上可以非常复杂。当程序员使用 decltype(exp) 获取类型时，编译器将根据以下三条规则得出结果：

*   如果 exp 是一个不被括号`( )`包围的表达式，或者是一个类成员访问表达式，或者是一个单独的变量，那么 decltype(exp) 的类型就和 exp 一致，这是最普遍最常见的情况。
*   如果 exp 是函数调用，那么 decltype(exp) 的类型就和函数返回值的类型一致。
*   如果 exp 是一个左值，或者被括号`( )`包围，那么 decltype(exp) 的类型就是 exp 的引用；假设 exp 的类型为 T，那么 decltype(exp) 的类型就是 T&。

为了更好地理解 decltype 的推导规则，下面来看几个实际的例子。

【实例 1】exp 是一个普通表达式：

```

#include <string>
using namespace std;

class Student{
public:
    static int total;
    string name;
    int age;
    float scores;
};

int Student::total = 0;

int  main(){
    int n = 0;
    const int &r = n;
    Student stu;

    decltype(n) a = n;  //n 为 int 类型，a 被推导为 int 类型
    decltype(r) b = n;     //r 为 const int& 类型, b 被推导为 const int& 类型
    decltype(Student::total) c = 0;  //total 为类 Student 的一个 int 类型的成员变量，c 被推导为 int 类型
    decltype(stu.name) url = "http://c.biancheng.net/cplus/";  //total 为类 Student 的一个 string 类型的成员变量， url 被推导为 string 类型

    return 0;
}
```

这段代码很简单，按照推导规则 1，对于一般的表达式，decltype 的推导结果就和这个表达式的类型一致。

【实例 2】exp 为函数调用：

```

//函数声明
int& func_int_r(int, char);  //返回值为 int&
int&& func_int_rr(void);  //返回值为 int&&
int func_int(double);  //返回值为 int

const int& fun_cint_r(int, int, int);  //返回值为 const int&
const int&& func_cint_rr(void);  //返回值为 const int&&

//decltype 类型推导
int n = 100;
decltype(func_int_r(100, 'A')) a = n;  //a 的类型为 int&
decltype(func_int_rr()) b = 0;  //b 的类型为 int&&
decltype(func_int(10.5)) c = 0;   //c 的类型为 int

decltype(fun_cint_r(1,2,3))  x = n;    //x 的类型为 const int &
decltype(func_cint_rr()) y = 0;  // y 的类型为 const int&&
```

需要注意的是，exp 中调用函数时需要带上括号和参数，但这仅仅是形式，并不会真的去执行函数代码。

【实例 3】exp 是左值，或者被`( )`包围：

```

using namespace std;

class Base{
public:
    int x;
};

int main(){
    const Base obj;

    //带有括号的表达式
    decltype(obj.x) a = 0;  //obj.x 为类的成员访问表达式，符合推导规则一，a 的类型为 int
    decltype((obj.x)) b = a;  //obj.x 带有括号，符合推导规则三，b 的类型为 int&。

    //加法表达式
    int n = 0, m = 0;
    decltype(n + m) c = 0;  //n+m 得到一个右值，符合推导规则一，所以推导结果为 int
    decltype(n = n + m) d = c;  //n=n+m 得到一个左值，符号推导规则三，所以推导结果为 int&

    return 0;
}
```

这里我们需要重点说一下左值和右值：左值是指那些在表达式执行结束后依然存在的数据，也就是持久性的数据；右值是指那些在表达式执行结束后不再存在的数据，也就是临时性的数据。有一种很简单的方法来区分左值和右值，对表达式取地址，如果编译器不报错就为左值，否则为右值。

## decltype 的实际应用

auto 的语法格式比 decltype 简单，所以在一般的类型推导中，使用 auto 比使用 decltype 更加方便，你可以转到《C++ auto》查看很多类似的例子，本节仅演示只能使用 decltype 的情形。

我们知道，auto 只能用于类的静态成员，不能用于类的非静态成员（普通成员），如果我们想推导非静态成员的类型，这个时候就必须使用 decltype 了。下面是一个模板的定义：

```

#include <vector>
using namespace std;

template <typename T>
class Base {
public:
    void func(T& container) {
        m_it = container.begin();
    }

private:
    typename T::iterator m_it;  //注意这里
};

int main()
{
    const vector<int> v;
    Base<const vector<int>> obj;
    obj.func(v);

    return 0;
}
```

单独看 Base 类中 m_it 成员的定义，很难看出会有什么错误，但在使用 Base 类的时候，如果传入一个 const 类型的容器，编译器马上就会弹出一大堆错误信息。原因就在于，`T::iterator`并不能包括所有的迭代器类型，当 T 是一个 const 容器时，应当使用 const_iterator。

要想解决这个问题，在之前的 C++98/03 版本下只能想办法把 const 类型的容器用模板特化单独处理，增加了不少工作量，看起来也非常晦涩。但是有了 C++11 的 decltype 关键字，就可以直接这样写：

```

template <typename T>
class Base {
public:
    void func(T& container) {
        m_it = container.begin();
    }

private:
    decltype(T().begin()) m_it;  //注意这里
};
```

看起来是不是很清爽？

注意，有些低版本的编译器不支持`T().begin()`这种写法，以上代码我在 VS2019 下测试通过，在 VS2015 下测试失败。