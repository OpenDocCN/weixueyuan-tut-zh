# OC 之谓词

你是否遇到过这样的困惑，例如：利用前面学习的知识从 plist 文件中获取到一个数组，但是这个数组中有些数据你不想要，只想保留对你有用的数据，那么该怎么做呢？

我们可能会想到很多种解决办法，例如一个一个挑出来，对于程序员的我们来说，那就太 low 了，我们需要创建一个方法将我们的数据挑选出来。

OC 的开发者们也想到了这个问题，所以为了提高我们的开发效率，给我们提供了一个专门应对这种状况的类——NSPredicate，中文翻译：谓词。

实际上，谓词就像是一个筛子(过滤器)，帮助我们将需要的数据按照一定的条件挑选出来。所以本节将围绕 NSPredicate 来介绍一下怎样在一堆数据中获取到想要的数据。

首先，先来学习一下如何创建谓词，通过查看 NSPredicate.h 接口中的方法，我们可以找到这个方法：

+(NSPredicate*)predicateWithFormat:(NSString*)predicateFormat,...;

首先，这个是一个类方法，它返回的是一个 NSPredicate 类的对象，方法中留给我们的是一个字符串，这个字符串用来填写过滤条件。过滤条件的书写下面只介绍几种常用的：

SELE：代表每个数据本身

=、==：是相同的，都是等于的意思。

BETWEEN：采用：表达式 BETWEEN{下限，上限}的格式

AND、&&、OR、NOT

BEGINSWITH：检查某个字符串是否以指定的字符串开头，如：BEGINSWITH’a’

CONTAINS：检查是否包含指定的字符串。

LIKE：用于筛选时都匹配指定的字符串格式，如：name LIKE ‘*ac*’表示 name 字符串中是否有 ac 字符串。

通过上面的方法以及参数的介绍，我们可以轻松创建一个谓词，那么怎么使用创建好的谓词呢？

在 NSPredicate 类中，还可以看到这么几个方法：

-(NSArray<ObjectType>*)filteredArrayUsingPredicate:(NSPredicate *)predicate;

从名字上就知道用于过滤 NSArray 的。

-(void)filterUsingPredicate:(NSPredicate*)predicate;//用于过滤 NSMutableArray 数组的

-(NSSet<ObjectType>*)filteredSetUsingPredicate:(NSPredicate *)predicate;//用于过滤集合(NSSet，集合，和数组一样，都是存储数组的方法，区别就是集合中元素互异，使用的地方不多，和 NSArray 及其类似，非常简单，大家自学，这里不在过多解释。)

通过这些方法，我们可以轻松地得到我们想要的数据。给大家一个例子：

首先创建一个 OC 工程，在工程中创建一个 Person 类：

person.h 中的代码：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject
@property (nonatomic,copy)NSString * name;
@property (nonatomic,assign)int age;
+(instancetype)personWithName:(NSString *)name age:(int)age;
@end
```

person.m 中的代码：

```
#import "Person.h"
@implementation Person
+(instancetype)personWithName:(NSString *)name age:(int)age{
    Person * person=[[Person alloc] init];
    person.name=name;
    person.age=age;
    return person;
}
@end
```

main.m 文件中的代码：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    NSArray * array=[NSArray arrayWithObjects:
                     [Person personWithName:@"ZhangSan" age:10],
                     [Person personWithName:@"LiSi" age:15],
                     [Person personWithName:@"WangWu" age:20],
                     [Person personWithName:@"WangLiu" age:25],
                     [Person personWithName:@"LiWu" age:30],
                     [Person personWithName:@"ZhangSi" age:35], nil];

    NSPredicate * predicate=[NSPredicate predicateWithFormat:@"age>20"];
   NSArray * myArray=[array filteredArrayUsingPredicate:predicate];
    for (Person * person in myArray) {
        NSLog(@"age>20 :%@",person.name);
    }
    return 0;
} 

```

输出结果：age>20 :WangLiu

age>20 :LiWu

age>20 :ZhangSi

(注意：在最后输出的时候，如果采用 NSLog 的方法，输出的是每个对象所占用的内存首地址，而不是具体的数据。大家可以上网搜索关于重写 NSLog 方法的代码，由于不属于 OC 的知识点，因此本文中不介绍)。

通过对谓词的学习，你是否看出使用谓词的弊端？通过谓词筛选的流程，我们可以看出，谓词过滤的是一些具有共性的数据，如果是杂乱没有规律的数据，我们如果使用谓词，反而会增加代码量。

所以，在实际编程过程中，要根据具体的环境，合理地选择实现地方式。