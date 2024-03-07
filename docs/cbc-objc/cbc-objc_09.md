# 第一个类程序

**最简单的****类**

首先，创建一个工程，命名为 Test，并将下面的代码编写到 main.m 中：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject
@property NSString * name;
-(void)display;
@end
//以上是类的声明部分
@implementation Person
-(void)display{
    NSLog(@"%@",self.name);
}
@end
//以上是类的实现部分
int main(int argc, const char * argv[]) {
    Person * person=[[Person alloc] init];
    person.name=@"ZhangSan";
    [person display];
    return 0;
}
```

运行结果：**ZhangSan**

在使用 C 语言或者 OC 语言中的基本数据类型时，需要遵循“先声明，后使用”的原则。学习 OC 的类时，同样也需要遵循一个原则：“先声明，再实现，后使用”。

**代码讲解**

**2-5 行代码－类的声明部分**

第 2、5 行代码：@interface 是声明类的一个关键字，Person 是声明类的类名，“：”可以理解成分隔符，后边紧跟的 NSObject 是这个类的父类。@end 是类声明结束的标志。在这个结构中，我们可以添加类的属性和方法。

（我们在 OC 中声明类的时候，必须指明父类（即使创建基类，也需要继承 NSObject 这个类）。

第 3 行代码：是向这个类中添加了一个字符串类型的 name 属性（声明属性的时候，不能给属性赋初值）：@property 是类中声明属性的关键字；在 OC 中，字符串类型用 NSString 类型的对象声明）（后面会具体讲 NSString）。

第 4 行代码:是向这个类中添加一个返回值类型为空的方法。（方法的讲解会在类的具体分析中介绍）

**7-11 行代码－类的实现部分**

第 7、11 行代码：是一个组合结构，这个结构是给声明好的类做实现用的。类声明和类实现的关系，就如同函数的声明和实现，光有声明，没有实现，那么声明的类或者函数就什么功能也没有，是没有意义的。

OC 中对方法的实现要写在上面这个结构中（在类的声明部分，只能声明属性和方法，不能对属性进行赋初值，也不能对方法做实现，这是类的实现部分的工作，OC 将这两个工作分开进行）。

8-11 行代码：给类中定义的 display 方法做的实现代码。实现的功能是输出类对象的 name 属性的值

（NSLog（）相当于 C 语言中的 printf 函数，@""相当于 printf 函数中的“”，%@是 OC 字符串类型的占位符，作用相当于 C 语言中输出语句中的％d，％s。通过 self.name 可以获取当前对象 name 属性的值）。

学到这，类就讲完了，大家可能会有许多疑惑：

1、self 是什么，self.name 是什么，为什么通过 self.name 就可以获得类对象的 name 属性的值？

2、为什么声明方法的前面一定要有一个“－”符号，看着怎么这么别扭？

……

等等这些问题，在本章的后面几节会讲到。

**13－18 行代码－Person 类的具体使用**

14 行代码：Person * person:是声明一个 Person 类的对象，但是光声明不行，OC 中的对象需要我们编写代码给它们分配内存空间。[Person alloc]中：alloc 是一个方法，它是从 NSObject 这个父类中继承过来的，它能为我们自动对 person 对象分配内存（分配多少有 OC 语言的底层管理，你不需要知道），init 同样是一个方法，也是继承父类的，它的功能是为 person 对象中的属性赋初值（表示数值的属性默认为 0，字符串的属性默认没有值（用 null 表示）。

14 行的代码可以替换成：（对于初始化对象的代码也可以这样写:

       Person * person=[Person alloc];(分配内存空间)

       person =[person init]（对 person 这个对象进行初始化）;

15 行代码：对 person 对象的 name 属性进行赋值。对于字符串来说，同样要使用@“”符号。

16 行代码：调用 person 对象的 display 方法；使用类创建的对象调用类中方法，采取的方式用［］扩起来，具体［］中的内容，在类的具体分析部分介绍。

（对类的使用做出的总结：

在使用类时（不管是我们自己创建的，还是 OC 语言自带的）：

如果需要调用对象的属性，采用：类对象.属性的方式如果需要调用类中的方法：对于方法开头有”－“的，用［对象 方法］的方法。在后面的学习中，我们还会遇到带有”＋“的，用［类名 方法］的方式调用）

通过对最简单的类程序进行分析，我们可以在 main.m 文件初步的创建一个简单的类。这种建立类的方式是不对的。

**OC****类的正确建立**

类的声明部分写在一个.h 文件中，类的实现部分写在.m 文件（不是 main.m）中。类的使用在 main.m 文件中。（创建文件的方法参照“OC 工程添加文件”章节）。

我们按照正确的做法将本节中的例子做一下拆分：

建立一个 Person.h 文件，编写以下代码：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject
@property NSString * name;
-(void)display;
@end
```

建立一个 Person.m 文件，编写以下代码：

```
#import "Person.h"
@implementation Person
-(void)display{
       NSLog(@"%@",self.name);
}
@end
```

在 main.m 文件中：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    Person * person=[Person alloc];
    person =[person init];
    person.name=@"ZhangSan";
    [person display];
    return 0;
}
```

运行结果是：

**ZhangSan**

注意：类的正确建立相比于前种方法，在类的实现部分和 main.m 文件中的使用部分都添加了类的.h 头文件。

使用这种方式建立类的好处是：（必须使用正确方式建立类）

1.  保护了类具有的封装的特性。使用前种方式使用类会破坏类的结构，做不到正确的使用类。
2.  正确地使用类编程，能够降低程度地耦合度，降低工程的维护工作量。同时，可以使类中实现代码不公开。