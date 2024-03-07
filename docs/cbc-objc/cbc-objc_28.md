# Block 代码块的使用

这一节学习新的语法－block 代码块。它是在 iOS4.0 和 Mac OS X10.6 以后的版本中才添加的，是对 C 语言的扩展。

简单的理解 block 代码块的作用就和 C 语言的函数的作用类似，就是将能够实现某种特定功能的一些代码包裹起来，留出必要的接口（就是参数），供外界调用。

知道 block 代码块的功能后，学习一下 block 的语法规则。

```
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
void (^helloWorld)(void);
     helloWorld=^(void){
     NSLog(@"Hello World!");
     };
    helloWorld();
    return 0;
}
```

运行结果：**Hello World!**

可以看到，上边的例子通过使用 block 代码块实现了“Hello World”小程序。分解一下 block 代码块，看看到底是怎么实现的。

第 4 行代码：是 block 的声明，第一个 void 是代码块的返回值类型，(^helloWorld)：“^”是 block 代码块的标志，无实际意义。helloWorld 是 block 代码块的名称，必须要用小括号扩起来。第二个 void 是代码块的参数，如果需要有参数，语法是：（int，……）或（int i，……）都可以；如果不需要参数，可以采用：（）或（void）但是不能连括号都不写。

6-8 行代码：是 block 代码块的实现。首先在对代码块实现的时候，“^”是必须存在的，（void）中需要写的是当代码块的参数。有参数时，在这里必须写上具体的每个参数名，如果没有参数，可以写成: （void）或（）或干脆省略都可以。后边大括号中的代码表示对 helloWorld 代码块的具体实现代码。

（提示：代码块的声明和实现可以写在一起）

代码块的调用和 C 语言的函数调用相同。通过：“代码块名（参数）”的方式。

在使用代码块的时候，对于全局变量，在块内是完全可操作的。

但是对于局部变量来说，在块内只能使用不能更改。

如果试图在块内更改局部变量的值，程序会报错，解决的方案是在声明局部变量时添加 __block 关键字（注意是两个“_”）：

创建一个工程，在 main.m 文件中：

```
#import <Foundation/Foundation.h>
int number=10;
int main(int argc, const char * argv[]) {
   __block int i=10;
    void(^block)()=^{
        NSLog(@"The i is :%d",i);
        NSLog(@"The number is :%d",number);
        i++;
        number++;
    };
    block();
    NSLog(@"The i is :%d",i);
    NSLog(@"The number is :%d",number);
    return 0;
}
```

运行结果：**The i is :10**

**           The number is :10**

**           The i is :11**

**           The number is :11**

(对于 Block 代码块的使用，它也可以和 C 语言中的函数一样，写在 main 函数的外面，但是必须遵循“先声明，后使用”的原则)

**代码块的具体使用**

通过上面的学习，应该基本上可以使用代码块了。在实际开发过程中，代码块的使用都出现在那些地方呢？

1、我们在使用 Foundation 框架中的某些方法时，可能会看到代码块，这些代码块作为方法的参数。例如：对 NSArray 类的数组进行排序可以使用代码块排序：

```
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
    NSArray * array=[NSArray arrayWithObjects:@"4",@"1",@"2",@"3",@"5", nil];
       array= [array sortedArrayUsingComparator:^NSComparisonResult(id obj1, id obj2) {
        int a1=[obj1 intValue];
        int a2=[obj2 intValue];
        return a1>=a2?-1:1;
    }];
       NSLog(@"%@",array);
       return 0;
}
```

运行结果：**(**

**    5,**

**    4,**

**    3,**

**    2,**

**    1**

**)**

通过上边对数组对象进行排序的例子，你会发现 block 代码块作为参数使用时，当使用这个排序方法时，代码块中的变量或对象是有值的。

通过将返回的数值进行比较，然后再返回给这个方法一个结果，就完成了对数组的排序。整个过程用到了 block 代码块的回调。

下面通过一个例子对这个过程进行一下模拟；

1、首先创建一个工程，例如：Demo；

2、创建一个 Person 类；

在 Person.h 中:

```
#import <Foundation/Foundation.h>
typedef void(^myBlock)(NSString * name,int age);
@interface Person : NSObject
-(void)exercise:(myBlock)block;
@end
```

其中，typedef 时 OC 中的关键字，表示重命名，作用是通过使用 typedef 关键字，简化较为复杂的类型声明，在后边使用代码块的时候，直接用代码块的名字代表就可以了。

在 Person.h 中，首先声明了一个全局的 block 代码块，并将之作为方法的参数。         

在 Person.m 文件中：

```
#import "Person.h"
@implementation Person
-(void)exercise:(myBlock)block{
    NSString * theName=@"ZhangSan";
    int age=10;
    block(theName,age);
}
@end
```

在这个文件中，对.h 声明的方法进行了实现，在实现过程中，我们调用了 block 代码块。直到现在我们还没有对 block 代码块做实现。

下面来看一下 main.m 文件中的代码：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    Person * person=[[Person alloc] init];
    [person exercise:^(NSString *name, int age) {
        NSLog(@"%@,%d",name,age);
    }];
    return 0;
}
```

在 main.m 文件中，我们看到，我们对 block 代码块做了实现，输出 name 值和 age 的值。通过给工程打断点，我们会看到这段小程序的运行过程：

首先，初始化一个 person 对象

向 person 对象发送 exercise：消息

运行 person.m 文件中的 exercise 实现部分的代码

运行到 block 代码块时，运行代码块的实现部分，也就是 main.m 文件中的输出语句

block 代码块的实现部分运行完后，程序继续运行 exercise 方法的实现部分。

exercise 方法的实现部分运行完后，表明向 person 对象发送的消息就完成了

然后运行 return 0 结束程序。

通过跟踪程序的执行顺序，我们可以看到 block 代码块作为参数时的程序运行流程。正是利用 block 代码块的这种运行机制，我们可以利用 block 完成传值的功能。

block 代码块，实际开发过程中，对于自定义的代码块很少用到，但是在 Foundation 框架中，block 代码块作为方法参数的情况比较多。

而且，虽然协议、通知等都有传值的功能，但是如果你能够灵活运用 block 代码块，比起前两种方式编程将变得更简单。