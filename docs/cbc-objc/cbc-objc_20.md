# OC 之 NSArray

OC 语言有自己的数组，和 C 语言的数组相比，OC 中的数组完全不是那个样子。一起学习一下。

OC 语言的数组就要用到 NSArray 这个类来声明，这是 Foundation 框架下的一个专门为数组而诞生的类。

**NSArray****的使用注意事项：**

1、数组中存储的必须是对象(OC 中的任意对象都可以，但是对于 C 语言中的 int、double、float 等不允许存储到 NSArray 数组里)。

2、数组中不允许存储 nil。(因为 NSArray 存储数据结束的标志就是 nil，如果在数组中存储了 nil，就会产生冲突，从而丢失数据)。

**创建 NSArray****的几种方式**

+ (instancetype)arrayWithObjects:(ObjectType)firstObj, ...

- (instancetype)initWithObjects:(ObjectType)firstObj, ...

对于上面两种方法，一个是类方法，一个是对象方法，都可以达到创建一个 NSArray 对象并初始化的目的。

例子：

```
#import <Foundation/Foundation.h>
int main() {
       NSArray * array1=[NSArray arrayWithObjects:@"0",@"1",@"2",@"3", nil];
       NSLog(@"array1 are :%@",array1);
       NSArray * array2=[[NSArray alloc] initWithObjects:@"00",@"11",@"22",@"33", nil];
       NSLog(@"array2 are :%@",array2);
       return 0;
}     
```

输出结果为：

**array1 are :(**

**    0,**

**    1,**

**    2,**

**    3**

**)**

**array2 are :(**

**    00,**

**    11,**

**    22,**

**    33**

**)**

**数组的****count**

同 NSString 类的 length 一样，NSArray 为我们提供了 count 属性和 count 方法。

例子：

```
#import <Foundation/Foundation.h>
int main() {
       NSArray * array1=[NSArray arrayWithObjects:@"0",@"1",@"2",@"3", nil];
       NSInteger count=[array1 count];//也可以用 array1.count
       NSLog(@"array1 are :%@ andTheCount is :%ld",array1,count);
       return 0;
}
```

输出结果：

**array1 are :(**

**    0,**

**    1,**

**    2,**

**    3**

**) andTheCount is :4**

**获取特定索引处的对象**

- (ObjectType)objectAtIndex:(NSUInteger)index;

例子：

```
#import <Foundation/Foundation.h>
int main() {
       NSArray * array1=[NSArray arrayWithObjects:@"0",@"1",@"2",@"3", nil];
       NSString * str=[array1 objectAtIndex:1];
       NSLog(@"%@",str);
       return 0;
}
```

输出结果为：1

分析：对于 OC 中 NSArray 的数组，和 C 语言中数组的索引是一样的，都是从 0 开始。所以，对于 array1 这个数组来说，索引为 1 的对象是@“1”。

注意：在使用索引时，一定要注意索引是否存在，如果索引不存在，就会产生越界的情况。例如对于 array1 这个数组来说，他的索引范围是 0-3(nil 只是作为结束标注，本身并不包括在数组中)，如果你引入的索引是 4，就会出错。所以，使用索引时一定要注意索引是否有意义。

**判断一个对象是否包含在一个数组中**

- (BOOL)containsObject:(ObjectType)anObject;

例子：

```
#import <Foundation/Foundation.h>
int main() {
       NSArray * array1=[NSArray arrayWithObjects:@"0",@"1",@"2",@"3", nil];
       NSLog(@"%d",[array1 containsObject:@"1"]);
       return 0;
}
```

输出结果：**1**

分析：由于该方法返回的是一个 BOOL 类型的值，之前我们介绍过 BOOL 类型的返回值如果是 1，说明是真的；反之，是假的。

除了以上为大家介绍的方法，NSArray.h 文件中还提供了实现各种功能的方法。我们大致可分为这么几类：

1、排序方法。

2、知道对象，获得该对象的索引的方法

3、获取某文件中的数组的方法。

……

这些方法我们会在后边的学习中都接触到。有兴趣的朋友可以进入 NSArray.h 文件中去尝试使用这些方法。

**NSMutableArray****的使用**

和 NSString 一样，除了有不可变的 NSArray，Foundation 还提供了可变的数组 NSMutableArray。可变数组比不可变数组增加了对数组中对象的增删改的功能：

**向数组中增加对象**

增加一个对象的方法：

- (void)addObject:(ObjectType)anObject;

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSMutableArray * array=[NSMutableArray arrayWithObjects:@"1",@"2",@"3", nil];
    [array addObject:@"4"];
    NSLog(@"%@",array);

    return 0;
}
```

输出结果：

**(**

**       1,**

**       2,**

**       3,**

**       4**

**)**

一次增加多个对象的方法：

- (void)addObjectsFromArray:(NSArray<ObjectType> *)otherArray;

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSMutableArray * array=[NSMutableArray arrayWithObjects:@"1",@"2",@"3", nil];
    NSArray * addArray=[NSArray arrayWithObjects:@"4",@"5",@"6", nil];
    [array addObjectsFromArray:addArray];
    NSLog(@"%@",array);
    return 0;
}
```

输出结果：

**(     **

**       1,**

**       2,**

**       3,**

**       4,**

**       5,**

**       6**

**)**

**删除数组中的对象**

删除数组中所有对象；

- (void)removeAllObjects;

删除某个对象：

- (void)removeObject:(ObjectType)anObject;

例子：

```
#import <Foundation/Foundation.h>
int main() {
       NSMutableArray * array=[NSMutableArray arrayWithObjects:@"1",@"2",@"3",@"4",@"5",@"6", nil];
    [array removeObject:@"2"];
    NSLog(@"the array is :%@",array);
    [array removeAllObjects];
    NSLog(@"the last array is :%@",array);
    return 0;
}
```

输出结果：

**the array is :(**

**                  1,**

**                  3,**

**                  4,**

**                  5,**

**                  6**

**              )**

**the last array is :(**

**                     )**

**对对象进行更改：**

```
#import <Foundation/Foundation.h>
int main() {
    NSMutableArray * array=[NSMutableArray arrayWithObjects:@"1",@"2",@"3",@"4",@"5",@"6", nil];
    [array replaceObjectAtIndex:2 withObject:@"7"];
    NSLog(@"%@",array);
    return 0;
}
```

输出结果：**(**

**1,**

**2,**

**7,**

**4,**

**5,**

**6**

**           )**

通过以上对 NSArray 以及 NSMutablArray 的介绍，应该能够简单的对 OC 中数组有个初步的了解，能够运行数组这种结构实现一些小功能。

对于类中提供的方法的讲解，由于可供选择的方法太多，我介绍的不够全面。在以后的不断学习中，如果用到某个方法再介绍。

通过对 NSString 和 NSArray 和学习，是否可以发现 Foundation 框架中方法声明的一些规律：

在 NSString 对象初始化时，我们用到了 stringWith…的方法。

在 NSArray 对象初始化时，我们用到了 arraywith… 的方法。

等等。

在以后的学习中，会接触更多的类。就可以运用积累的这些经验来“蒙”代码。在不断的深入了解 Foundation 框架的过程中，你会发现更多的规律。

所以不介意初学者死记代码，那样做对以后的发展不利。要在学习的同时，自己不断摸索出它的规律，对自己的帮助会更大。当然，这只是我自己对 Foundation 的理解，和朋友们分享一下，希望可以帮助到你们。