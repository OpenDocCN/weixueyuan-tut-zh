# OC 中的单例

单例，简单的语言概括，就是唯一的一个实例，也就是唯一的一个对象，大家都对同一个对象进行操作。

在 Foundation 框架中，很多的类中都用到了单例这种编程模式，它是处理数据共享的最常用的一种方法。

**单例的理解**

简单的理解，就是在开发过程中，有时会需要在不同的地方对同一块内存的数据进行操作，而对于 OC 来讲，这块内存往往代表的是一个对象的数据。

所以想出了这样一种办法：就是可以通过一个类方法直接返回一个本类的对象(必须是类方法，因为如果是对象方法，在调用的时候还要声明一个该类的对象并对其初始化，而这个对象本身没有实际作用，无故的占用了内存空间)，而且保证这个对象只被分配一次内存空间(这样才能保证通过这个对象永远能够对那块内存空间进行操作)，通过这种方式，在整个工程中，你只要想读取这块内存中的数据，就可以通过调用该类的类方法返回这个唯一的对象。

**单例的实例**

这个例子模拟的是两个人 ZhangSan 和 LiSi 合租了一辆车，通过记录每个人开车的时间，最后统计出这辆车总共跑的小时数。

创建一个工程，例如：Share；

创建两个类，一个是 Person 类；一个是 Car 类；

由于两个人共用一辆车，所以 Car 类中有一个单例，我们先来看 Car 类的代码实现：

Car.h 中的代码：

```
#import <Foundation/Foundation.h>
@interface Car : NSObject
@property (nonatomic,assign)int driveHours;
+(instancetype)car;
@end
```

Car.m 中的代码：

```
#import "Car.h"
static Car * car=nil;
@implementation Car
+(instancetype)car{
         if (car==nil) {
             car=[[Car alloc] init];
         }
         return car;
}
@end
```

分解一下 Car 类中的代码：

Car.h 中声明了一个纪录总时间的属性和一个类方法。

关键的代码在 Car.m 中，首先我们声明了一个全局本类的对象（这个对象必须赋空值）：

static 关键字在这里的作用是：使这个对象只分配一次内存(static 还有很多特性)。

在 car 的类方法中，我们首先判断 car 对象是否是空值，如果是空值，我们才给它初始化。否则，说明它已经被使用过了，就直接返回，不进入 if 语句。

下面，我们来看一下 Person 类中的代码实现：

Person.h 的代码：

```
#import <Foundation/Foundation.h>
#import "Car.h"
@interface Person : NSObject
@property (nonatomic,copy)NSString * name;
@property (nonatomic,strong)Car * car;
-(void)displayWithDriveHours:(int)hours;
@end
```

Person.m 中的代码：

```
#import "Person.h"
@implementation Person
-(instancetype)init{
if (self=[super init]) {
        self.car=[Car car];//创建对象的 car 属性实际上就是 Car 类的那个唯一的实例。
        self.name=nil;
    }
    return self;
}
-(void)displayWithDriveHours:(int)hours{
    self.car.driveHours+=hours;
}
@end
```

main.m 文件中代码：

```
import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    Person * ZhangSan=[[Person alloc] init];

    Person * LiSi=[[Person alloc] init];

    [LiSi displayWithDriveHours:5];

    [ZhangSan displayWithDriveHours:10];

    [LiSi displayWithDriveHours:3];
    Car * car=[Car car];
    NSLog(@"The allHours are %d",car.driveHours);
    return 0;
}
```

输出结果：**The allHours are 18**

在 main.m 中，无论是哪个对象调用这个类方法，返回的都是相同的对象，对同一块内存数据做操作。这就是单例的实际作用。

我们已经学习了很多方法传递数据了，每种方式有它特有的优势，这个需要在不断地实践中，体会它们，在适当的地方使用适当的传递方法。

单例，严格来说应该是工程中需要共享数据时才使用，如果用单例只是去传递数据，那就有点大材小用了。