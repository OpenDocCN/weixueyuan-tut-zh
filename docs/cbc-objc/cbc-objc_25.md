# OC 中的通知

在现实生活中，传递信息的方式用的最多的就是通知，而且随着信息时代的发展，有时一次可以通知多人。

OC 语言为了在编程中实现这种一对多通信的模式，采取了通知中心这样一种机制。简单的理解就是，首先希望取得通过的对象提前到通知中心注册想要获取的通知。这样当某对象将想要发出的通知传递到通知中心时，通知中心就会立刻根据之前注册过的信息将通知发给想要这个通知的所有人。

在 OC 的通知中规定：等待通知下达的被称作“观察者”。

首先要清楚的是，使用通知时，要先有消息的观察者，后有消息的发送者(如果反过来，由于 OC 中消息的发送和接受是立刻执行的，观察者将接受不到通知，发送消息也就变得没有任何意义)

明白了通知是什么以及通知需要注意的几点之后，我们通过一个小例子来学习一下通知。

这个例子是说老板发出一个通知，内容是让他的工人去生产小汽车，工人们收到这个消息后，开始工作，具体实现如下：

首先，我们新建一个工程，例如：Demo3；

在工程中，我们新建一个 Worker 类，代表工人。

Worker.h 文件中没有添加新的代码。

Worker.m 文件中：

```
#import "Worker.h"
@implementation Worker
-(instancetype)init{
    if (self=[super init]) {
        [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(makeCar) name:@"canMake" object:nil];
    }  
    return self;
}
-(void)makeCar{
    NSLog(@"Let's begin to make car,gogogo");
}
@end
```

代码讲解：

4-9 行代码：是 init 方法，大家在之前的学习中可能没见过，但是大家每次对类对象进行初始化时都会用到它。在编写代码时，如果你不写它，在对象初始化时也会调用它。这个方法是继承自 NSObject 父类的。

第 5 行代码：涉及到了类之间的继承。由于每个类都含有父类，所以每个类中，都包含有父类的属性和方法。在进行初始化时，同样也需要对父类中的属性进行初始化。所以，这里通过调用 super 的 init 方法，首先对父类进行初始化，然后如果本类属性需要进行初始化，再做设置。

第 6 行代码：大家要知道这是一段用于监听的代码。在这做监听，是因为，在创建每一个工人对象时，该工人都要等待老板的通知。所以在初始化中做这个工作最适合不过了。

知道为何在 init 方法中进行对通知的监听后，分析一下监听的代码写法：

NSNotificationCenter 是一个类，用于通知的收集和发送。defaultCenter 是一个类方法，返回的是这个类的对象，而且每次调用这个类方法的时候，返回的都是这个对象(单例)。后边的 addObserver…，是生成观察者的方法，其中的 self 参数是监听者对象，@selector 是当创建的观察者接收到通知后要执行的方法，name 参数表示要监听的消息的名称，object 是通知发送的时候传递过来的参数对象，一般为 nil。

return self;返回本类的对象。

main.m 文件中的代码：

```
#import <Foundation/Foundation.h>
#import "Worker.h"
int main(int argc, const char * argv[]) {
    Worker * worker=[[Worker alloc] init];
[[NSNotificationCenter defaultCenter]postNotificationName:@"canMake" object:nil];
    [[NSNotificationCenter defaultCenter]removeObserver:worker name:@"canMake" object:nil];
    return 0;
}
```

输出结果：**Let's begin to make car,gogogo**

代码讲解：

第 5 行代码：首先先实例化了一个 worker 类，但是别忘了，在初始化 init 方法中增加了监听者。

第 6 行代码：这行代码是发送消息的代码，[NSNotificationCenter defaultCenter]同样是通过调用 defaultCenter 类方法来获得唯一的那个对象，然后调用 post 的方法将名称为 canMake 的消息发送出去，object 同样是 nil。

注意：由于前面提到的通知的用法是先有观察者，后有消息的发送者，所以如果将 5、6 代码的顺序调整，运行程序虽然不会报错，但是不会输出内容。所以在通知的使用时一定要注意观察者和消息的发送者之间的顺序关系。

第 8 行代码：在使用通知的时候，一定要注意及时将观察者移除，通过调用 removeObserver 方法就可以将 worker 这个对象中的监听者移除。

提示：在使用通知的时候，要注意将创建的监听者手动移除（使用后马上移除，避免程序出错），养成一个良好的习惯。