# OC 之 NSDate

NSDate 类是用来获取时间的一个类，在我们实际开发中有时会需要获取当前的时间，这时就需要使用这个类。

NSDate 类获取当前时间的小例子：

```
#import <Foundation/Foundation.h>
int main() {
       NSDate * date=[NSDate date];
       NSLog(@"%@",date);
       return 0;
}
```

输出结果：**2016-02-29 01:49:51 +000(****输出结果和你运行此程序时的时间对应即为正确输出****)****。**

从这个小例子中，可以提取到一个重要的方法：

+ (instancetype)date;

这个方法主要用来获取时间然后返回给 NSDate 类的对象的。而且这是一个类方法，所以需要通过[NSDate date]的方法调用。

通过查看 NSDate 类接口文件中提供的方法，会发现供我们经常使用的方法很少，而且似乎还缺少点什么，例如，如果我想对获取的时间进行格式上的设置，发现根本没有。

NSDate 类中没有，并不代表别的类中没有。和 NSDate 经常一起使用的还有一个类，“NSDateFormatter”。这个类就是专门设置获取的时间对象的。

通过一个小例子，来看一下 NSDateFormatter 发挥的功能：

```
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
    NSDate * date=[NSDate date];
    NSDateFormatter * formatter=[[NSDateFormatter alloc]init];
    [formatter setDateFormat:@"YYYY-MM--dd"];
    NSString * string=[formatter stringFromDate:date];
NSLog(@"%@",string);
[formatter setDateFormat:@"YYYY-MM--dd hh:mm"];
    string=[formatter stringFromDate:date];
    NSLog(@"%@",string);
    return 0;
}
```

运行结果：**2016-03—17**

**       2016-03--17 04:48****（以运行时间为准）**

分析一下这段代码：

首先获取到了当前时间

然后创建了一个 formatter 的对象，通过这个类提供的 setDateFormat：方法，可以对这个对象本身进行时间格式的设置。（对于参数的写法，大家可以去网上搜索，有很多）。

然后通过 formatter 对象调用它自身的 stringFromDate 方法将获取到的时间设置完时间格式病转换成字符串。最后输出。

后边，又换了一种日期格式，再进行一次输出。

在日常的使用中，对于 NSDate 操作最多的就是获取时间，通过 NSDateFormatter 类提供的方法进行格式上的设置。