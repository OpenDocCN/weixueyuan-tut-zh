# OC 中的协议

**协议的理论理解**

OC 语言是单继承多协议的语言。在学习继承的同时，还需要学会适当的使用协议完成特定的功能。

协议和我们生活中的含义大同小异，例如：有一个孩子，由于年龄很小，无法照顾自己，所以不得不委托他人来照顾他的饮食起居。

从协议的角度分析上面的例子，孩子需要得到照顾，但是他自己办不了，只能让保姆照顾他。所以孩子是协议生成方，保姆是协议实现方。

从程度的角度上考虑，协议就是某类创建，其他类来帮其实现。

**使用协议需要注意的点**

1、协议没有父类(协议可以服从多个(>=0)协议（一般服从 NSObject 协议）。

(这里的 NSObject 是 NSObject.h 文件中的定义的一个协议，并不是 NSObject 这个类，如果有疑问，大家可以 command 点击进入 NSObject 这个协议，顺便了解一下这个协议中所包含的方法)

2、协议中不能定义变量（属性），只能定义方法。

**协议的创建**

1、创建一个 OC 工程，例如：Demo2，

2、在此工程中添加一个 Children 类，

3、然后再添加一个 Nanny(保姆)类，

3、在 Children 这个类中添加一个协议，代码如下；

Children.h 文件：

```
#import <Foundation/Foundation.h>
@protocol ChildrenDelegate <NSObject>//协议声明
-(void)eat;
@end
@interface Children : NSObject
@property(nonatomic,weak)id<ChildrenDelegate>delegete;
@end   

```

2-4 行代码－－协议的生成部分：

@protocol 是定义协议的关键字，告诉编译器，要开始声明协议了。

ChildrenDelegate 是协议的名字，规范命名为：类名+Delegate。

<NSObject>是一个协议，其中提供了很多方法供我们使用，通过 main.m 中代码的解释我们会看到服从这个协议的好处。（协议用“<>”）

@end 告诉编译器我的协议声明完了。

在协议中，有几个方法的修饰词供我们使用：

@required(表示以下方法必须实现)

@optional(表示以下方法可以不实现)

如果不使用任何关键字修饰，默认情况下是@required。

例如在这个协议中：

-(void)eat;

这个方法没有用关键字修饰，编译器默认情况下，认为它是必须实现的方法。

我们还可以向协议中添加带有@required 和@optional 关键字修饰的方法：

@required

-(void)takeCare;

这个方法用@required 修饰，所以是协议实现方必须实现的方法。     

@optional

-(void)cook;

这个方法用@optional 修饰，表明协议实现方可以选择是否实现这个协议。

第 6 行代码－－delegate 的创建

之前讲过，协议是需要让其他人来帮你实现的，那么就产生了一个问题：怎么规定这个帮你实现方法的人是谁呢？

为了解决这个问题，需要声明这么一个属性来确定谁是代理实现方。

@property 是定义属性的关键字。

nonatomic 表明这个属性是非原子性的，加快了运行效率;weak 是弱引用，是使用协议时用到的属性。

id：前面提到，可以表示任何数据类型，使用 id 的原因是无法确定谁会成为这个代理的实现方。

<ChildrenDelegate>表明这个属性方法和这个协议相关联。

delegete：是代理属性名(代理属性名的命名也是有潜规则的，一般命名为 delegate，表明这是一个代理属性。还有一个原因就是因为 Foundation 框架中的代理属性也是这么命名的，所以我们为了让自己变得专业，也要和专业开发人员学习。如果一个类中还需要一个代理，就命名为 dataSource)，一般一个类最多也就有两个协议。

通过上面的工作，我们在 Children 这个文件中，定义了一个协议，并将这个协议和我们的类进行关联(由于我们在类中声明了和协议相关联的属性，所以使整个类都和协议产生了关联，如果有些疑惑，下面的代码可能会帮助到你)。

**协议的实际应用**

下面我们在 nanny 这个类中实现这个协议，首先来看代码：

Nanny.h 文件中的代码:

```
#import <Foundation/Foundation.h>
#import "Children.h"
@interface Nanny : NSObject<ChildrenDelegate>
@end
```

代码解释：

<ChildrenDelegate>表明这个类服从 ChildrenDelegate 这个协议(<>中还可以添加多个协议，中间用逗号隔开。)

如果一个类服从一个协议，那么这个类就要实现协议中的方法。

Nanny.m 文件中的代码:

```
#import "Nanny.h"
@implementation Nanny
-(void)eat{
    NSLog(@"the nanny is taking care of the child eating something!");
}
@end
```

**协议的具体应用**

通过前面的学习，完成了协议声明方和实现方的代码实现，下面学习使用协议来完成一定功能。

main.m 中的代码实现：

```
#import <Foundation/Foundation.h>
#import "Children.h"
#import "Nanny.h"
int main(int argc, const char * argv[]) {
    Children  * child=[[Children alloc] init];
    Nanny * nanny=[[Nanny alloc] init];
    child.delegete=nanny;
    if ([child.delegete respondsToSelector:@selector(eat)]) {
        [child.delegete  eat];
    }
    return 0;
}
```

输出结果：

**the nanny is taking care of the child eating something!**

代码讲解：

首先，要运用协议，我们要引入代理声明方和实现方。(为什么呢？只引入实现方不行吗？你会发现，是可行的。能这样做，是因为在 Nanny 类中引入了 Children 类，所以.m 中只需引入 Nanny 类就可以了。

之所以要重复引入这个类，是因为这样增强了代码的可读性，而且由于@import 的特性(不管引入多少次，一个类只引入一次)，并没有降低代码的运行效率)。

在 5-7 行代码中，首先声明了两个类的对象，这是为第 7 行做准备，由于代理声明方要确定代理实现方是哪个对象，所以要使用到之前使用到的代理属性。所以使用点语法来为代理属性进行赋值。

为什么赋值的是类对象呢？这个很好理解，首先别人要帮你完成任务，首先它必须是一个实体，也就是对象。

8-10 行代码是要重点理解的，child.delegete 其实就是 nanny 对象，那么为什么不直接使用 nanny 呢？在简单程序中是可以的，如果在复杂的程序中，使用实现方对象的前提是你必须获得这个对象，往往费时费力。所以为了避免这种情况，一般使用上面这种方式，而且代码读起来非常清晰，易于理解。

respondsToSelector:方法：这个方法是 NSObject 协议中的方法，功能是判断代理实现方是否已经实现了代理中的某个方法。

使用这个方法的好处就是，如果代理实现方没有实现这个代理方法，整个工程不至于崩溃(如果不使用这个方法做判断，一旦你调用了只声明没有实现的方法，程序将会出错。这也就解释了为什么创建协议的时候，默认至少该协议要服从 NSObject 这个协议了)。

@selector():专门在调用方法时用到，如果你看到某个方法的参数是 SEL 类型时，就说明这需要传进去一个方法，书写语法为：@selector(方法)（这里的方法是简写的形式，就是不显示参数名）。

结合上面的解释，总体的来解释这个 if 语句，如果代理实现方实现了 eat 这个代理方法，那么就执行[child.delegete  eat]，也就是调用 nanny 对象的 eat 方法。

那么，问题就来了， eat 方法没有在 Nanny.h 接口文件中声明，只是在.m 文件中做了实现。为什么外界能调用呢？

这和协议有关系，一旦某个类服从了某个协议，那么该类就需要实现协议中规定必须实现的方法。而这些方法可以通过［协议生成方.delegate 方法］的方式调用，

**协议可以实现的功能**

明白协议的代码编写规则之后，还需要了解协议在实际开发过程中的作用。由于协议本身在本文件中声明，而其方法却在其他类文件中实现的这一特性，可以进行两个类之间的数据传递(实现的具体方式就是将数据传递给协议中的方法参数，通过方法的“异地”执行，将数据传递过去)。