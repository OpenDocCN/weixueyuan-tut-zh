# OC 中的包裹类

通过前面对 Foundation 框架中 NSArray、NSDictionary 的学习，对于 OC 语言中的存储类型所存储的都是对象，C 语言中的 int 等基本数据类型无法存到数组、字典中。有什么办法能解决这个问题呢？

其实 Foundation 框架中有专门解决这个问题的类－NSNumber 类和 NSValue 类，可以借助两个类实现类型之间的转换。

**NSNumber 类**

NSNumber 类实现的功能是 C 语言中的基本数据类型和 OC 对象相互转换。

NSValue 类实现的功能是 C 语言声明的结构体和 OC 对象相互转换。

下面先来学习一下 NSNumber 的具体使用：

```
#import <Foundation/Foundation.h>
int main() {   
    int number=5;
    NSNumber * intNum=[[NSNumber alloc] initWithInt:number];
    //将对象 5 存储到数组中。
    NSArray * array=[NSArray arrayWithObjects:intNum, nil];
    //输出 array 数组中的对象
NSLog(@"the array is :%@",array);
//从数组中得到对象 5，由于数组中只有一个对象 5，所以索引是 0
NSNumber * getNum=[array objectAtIndex:0];
//重新得到 int 类型的值
    int getNumber=[getNum intValue];
    NSLog(@"the number is :%d",getNumber);
    return 0;
}
```

输出结果是：

**the array is :(**

**    5**

**)**

**the number is :5**

**NSValue 类****的使用**

对于 NSValue 来说，它的作用是可以将 C 语言中的结构体转换成对象，反之，将结构体对象转换成结构体也可以。

目前我们接触过的关于 Foundation 框架中的结构体只有 NSRange，我们还会接触到 NSPoint(记录某个点的坐标的结构体，包含 X 和 Y 两个浮点型的变量)、NSSize(包含 width 和 height 两个浮点型变量)、NSRect(包含 NSPoint 类型的变量 origin 和 NSSize 类型的变量 size，一般定义四边形的时候用)。

以上四个结构体，我们目前只需掌握 NSRange 即可，其他三个以后碰到会讲。

例子：

```
#import <Foundation/Foundation.h>

int main() {

    //定义一个 NSRange 类型的变量
    NSRange range;
    range.length=2;
    range.location=1;

    //将 range 转换成对象
    NSValue * value=[NSValue valueWithRange:range];

    //将 NSRange 对象转换成 NSRange 变量
    NSRange theRange=[value rangeValue];

    //输出这个结构体中的数据，(unsigned long)是强制类型转换，和 C 语言类似
    NSLog(@"%lu,%lu",(unsigned long)theRange.length,(unsigned long)theRange.location);

    return 0;
}
```

**输出结果：****2,1**

在这两个包裹类中，还有一些方法(一般用这两个类进行类型转换时使用，很少单独用这两个类)，如果感兴趣，可以参考文档。

通过本章的讲解，Foundation 框架中的常用数据类型基本上就讲完了，还有两个比较常用的类型：日期类(NSDate)和二进制类(NSData)比较简单，会在讲解过程中给大家介绍。