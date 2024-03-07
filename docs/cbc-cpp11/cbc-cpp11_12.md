# C++11 非受限联合体（union）

> 原文：[`c.biancheng.net/view/7165.html`](http://c.biancheng.net/view/7165.html)

在 C/C++ 中，联合体（Union）是一种构造数据类型。在一个联合体内，我们可以定义多个不同类型的成员，这些成员将会共享同一块内存空间。老版本的 C++ 为了和 C 语言保持兼容，对联合体的数据成员的类型进行了很大程度的限制，这些限制在今天看来并没有必要，因此 C++11 取消了这些限制。

C++11 标准规定，任何非引用类型都可以成为联合体的数据成员，这种联合体也被称为非受限联合体。例如：

```

class Student{
public:
    Student(bool g, int a): gender(g), age(a) {}
private:
    bool gender;
    int age;
};

union T{
    Student s;  // 含有非 POD 类型的成员，gcc-5.1.0  版本报错
    char name[10];
};
int main(){
    return 0;
}
```

上面的代码中，因为 Student 类带有自定义的构造函数，所以是一个非 POD 类型的，这导致编译器报错。这种规定只是 C++ 为了兼容 C 语言而制定，然而在长期的编程实践中发现，这种规定是没有必要的。

关于 POD 类型稍后我们会讲解，大家先不要着急。

接下来，我们具体看一下 C++11 对 C++98 的改进。

#### 1\. C++11 允许非 POD 类型

C++98 不允许联合体的成员是非 POD 类型，但是 C++1 1 取消了这种限制。

POD 是 C++ 中一个比较重要的概念，在这里我们做一个简单介绍。POD 是英文 Plain Old Data 的缩写，用来描述一个类型的属性。

POD 类型一般具有以下几种特征（包括 class、union 和 struct 等）：

1) 没有用户自定义的构造函数、析构函数、拷贝构造函数和移动构造函数。

2) 不能包含虚函数和虚基类。

3) 非静态成员必须声明为 public。

4) 类中的第一个非静态成员的类型与其基类不同，例如：

class B1{};
class B2 : B1 { B1 b; };

class B2 的第一个非静态成员 b 是基类类型，所以它不是 POD 类型。

5) 在类或者结构体继承时，满足以下两种情况之一：

*   派生类中有非静态成员，且只有一个仅包含静态成员的基类；
*   基类有非静态成员，而派生类没有非静态成员。

我们来看具体的例子：

class B1 { static int n; };
class B2 : B1 { int n1; };
class B3 : B2 { static int n2; };

对于 B2，派生类 B2 中有非静态成员，且只有一个仅包含静态成员的基类 B1，所以它是 POD 类型。对于 B3，基类 B2 有非静态成员，而派生类 B3 没有非静态成员，所以它也是 POD 类型。

6) 所有非静态数据成员均和其基类也符合上述规则（递归定义），也就是说 POD 类型不能包含非 POD 类型的数据。

7) 此外，所有兼容 C 语言的数据类型都是 POD 类型（struct、union 等不能违背上述规则）。

#### 2\. C++11 允许联合体有静态成员

C++11 删除了联合体不允许拥有静态成员的限制。例如：

```

union U {
    static int func() {
        int n = 3;
        return n;
    }
};
```

需要注意的是，静态成员变量只能在联合体内定义，却不能在联合体外使用，这使得该规则很没用。

## 非受限联合体的赋值注意事项

C++11 规定，如果非受限联合体内有一个非 POD 的成员，而该成员拥有自定义的构造函数，那么这个非受限联合体的默认构造函数将被编译器删除；其他的特殊成员函数，例如默认拷贝构造函数、拷贝赋值操作符以及析构函数等，也将被删除。

这条规则可能导致对象构造失败，请看下面的例子：

```

#include <string>
using namespace std;

union U {
    string s;
    int n;
};

int main() {
    U u;   // 构造失败，因为 U 的构造函数被删除
    return 0;
}
```

在上面的例子中，因为 string 类拥有自定义的构造函数，所以 U 的构造函数被删除；定义 U 的类型变量 u 需要调用默认构造函数，所以 u 也就无法定义成功。

解决上面问题的一般需要用到 placement new（稍后会讲解这个概念），代码如下：

```

#include <string>
using namespace std;

union U {
    string s;
    int n;
public:
    U() { new(&s) string; }
    ~U() { s.~string(); }
};

int main() {
    U u;
    return 0;
}
```

构造时，采用 placement new 将 s 构造在其地址 &s 上，这里 placement new 的唯一作用只是调用了一下 string 类的构造函数。注意，在析构时还需要调用 string 类的析构函数。

#### placement new 是什么？

placement new 是 new 关键字的一种进阶用法，既可以在栈（stack）上生成对象，也可以在堆（heap）上生成对象。相对应地，我们把常见的 new 的用法称为 operator new，它只能在 heap 上生成对象。

placement new 的语法格式如下：

new(address) ClassConstruct(...)

address 表示已有内存的地址，该内存可以在栈上，也可以在堆上；ClassConstruct(...) 表示调用类的构造函数，如果构造函数没有参数，也可以省略括号。

placement new 利用已经申请好的内存来生成对象，它不再为对象分配新的内存，而是将对象数据放在 address 指定的内存中。在本例中，placement new 使用的是 s 的内存空间。

## 非受限联合体的匿名声明和“枚举式类”

匿名联合体是指不具名的联合体（也即没有名字的联合体），一般定义如下：

union U{
    union { int x; };  //此联合体为匿名联合体
};

可以看到，联合体 U 内定义了一个不具名的联合体，该联合体包含一个 int 类型的成员变量，我们称这个联合体为匿名联合体。

同样的，非受限联合体也可以匿名，而当非受限的匿名联合体运用于类的声明时，这样的类被称为“枚举式类”。示例如下：

```

#include <cstring>
using namespace std;

class Student{
public:
    Student(bool g, int a): gender(g), age(a){}
    bool gender;
    int age;
};

class Singer {
public:
    enum Type { STUDENT, NATIVE, FOREIGENR };
    Singer(bool g, int a) : s(g, a) { t = STUDENT; }
    Singer(int i) : id(i) { t = NATIVE; }
    Singer(const char* n, int s) {
        int size = (s > 9) ? 9 : s;
        memcpy(name , n, size);
        name[s] = '\0';
        t = FOREIGENR;
    }
    ~Singer(){}
private:
    Type t;
    union {
        Student s;
        int id;
        char name[10];
    };
};
int main() {
    Singer(true, 13);
    Singer(310217);
    Singer("J Michael", 9);

    return 0;
}
```

上面的代码中使用了一个匿名非受限联合体，它作为类 Singer 的“变长成员”来使用，这样的变长成员给类的编写带来了更大的灵活性，这是 C++98 标准中无法达到的（编译器会报`member 'Student Singer::<anonymous union>::s' with constructor not allowed in union`错误）。