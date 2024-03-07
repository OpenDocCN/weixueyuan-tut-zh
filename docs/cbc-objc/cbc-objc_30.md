# 本地存储对象的方法

本节我们讲学习如何将创建的对象存储到本地文件中。存储对象，Foundation 框架中提供了一个专门实现这个功能的类：NSKeyedArchiver 和 NSKeyedUnarchiver。

通过这两个类，我们就能够轻松地实现对对象进行存储和读取的功能，OC 中对于对象的本地存储和读取有另外的称呼：

将对象存储到本地文件中被称作：归档。

从本地文件中读取数据的操作被称为：解档或者恢复。

通过归档和解档，可以实现将 OC 中的几种数据类型（必须转换成对象类型才能存储）或者是多个自定义对象保存到本地文件中，反之也可以。

下面通过例子来介绍一下，要想实现功能所要注意的细节：

创建一个工程，例如功能名为：Demo4；

创建完成之后，新建一个类，例如：Person 类。

在 Person 类中添加几个属性或方法(随意，可以仿照例子)

做完上述准备工作后，我们需要让 Person 类中服从 NSCoding 协议：Foundation 框架规定，对于每一个要实现将自定义对象存储到本地文件的类，都要服从 NSCoding 协议。

既然服从了 NSCoding 协议，就要实现协议中的用@required 修饰的方法(简单的理解，就是如果要将一个对象进行归档或者解档，那么这个对象中的属性也必须要进行归档或者解档)，所以这个协议中需要进行实现的只有两个方法；

-(void)encodeWithCoder:(NSCoder *)aCoder ：从方法的命名就可以看出，这是归档的方法。

-(instancetype)initWithCoder:(NSCoder *)aDecoder ：这是解档用到的方法。

下面来看一下具体的代码；

Person.h 中的代码：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject<NSCoding>
@property (nonatomic,copy)NSString * name;
@property (nonatomic,assign)int age;
@property (nonatomic,assign)BOOL sex;
@end
```

Person.m 中的代码：

```
#import "Person.h"
@implementation Person
-(void)encodeWithCoder:(NSCoder *)aCoder{
//在这个方法中要对类中声明的每个属性都要进行归档。
    [aCoder encodeObject:self.name forKey:@"name"];
    [aCoder encodeBool:self.sex forKey:@"sex"];
[aCoder encodeInt:self.age forKey:@"age"];
}
-(instancetype)initWithCoder:(NSCoder *)aDecoder{
    self.name=[aDecoder decodeObjectForKey:@"name"];
    self.sex=[aDecoder decodeBoolForKey:@"sex"];
    self.age=[aDecoder decodeIntForKey:@"age"];
//通过查找键找出对应的属性值，而这个键值就是在归档时候设置的值。
return self;
}
@end
```

**归档一个对象**

通过代码中解释，对于 NSCoding 协议应该有了一个基本的认识。通过上面的工作，Person 类的代码已经编写完成了，下面我们通过一个例子来看一下怎么通过归档的方式将 Person 类的对象保存到本地文件中：

在 main.m 文件中编写以下代码：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    Person * person1=[[Person alloc] init];
    person1.name=@"ZhangSan";
    person1.sex=1;
    person1.age=10;
    NSString * path=@"/Users/xieyang/Desktop/a.txt";
    [NSKeyedArchiver archiveRootObject:person1 toFile:path];//这行代码将 person1 对象归档到 path 这个路径下的文件中
    return 0;
}
```

（提示：在 mac 下，如果你想知道某个文件的绝对路径，有一个简便方法：打开命令行，将文件直接拖到命令行下，你就可以直接得到这个文件的绝对路径）

**解档一个对象**

通过运行上边的代码，完成了将 person1 对象存储到 a.txt 文件中，下面来读取一下，尝试从 a.txt 文件中得到 person1 对象：

在 main.m 文件中编写以下代码：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    NSString * path=@"/Users/xieyang/Desktop/a.txt";
    Person * person=[NSKeyedUnarchiver unarchiveObjectWithFile:path];//在解档的时候，我们首先要找到文件，然后通过这个方法直接提取文件中的的对象
    NSLog(@"the person's name is :%@,sex is :%d,age is :%d",person.name,person.sex,person.age);
    return 0;
}
```

输出结果：**the person's name is :ZhangSan,sex is :1,age is :10**

**归档多个对象**

通过以上内容的学习，掌握了关于如何存储一个对象，那么如何储存多个对象呢，我们来继续学习一下：

在 main.m 文件中编写以下代码：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    Person * person1=[[Person alloc] init];
    person1.name=@"ZhangSan";
    person1.sex=1;
    person1.age=10;

    Person * person2=[[Person alloc] init];
    person2.name=@"LiSi";
    person2.sex=1;
    person2.age=20;
    //存储文件的路径
    NSString * path=@"/Users/xieyang/Desktop/a.txt";
    NSMutableData * data=[NSMutableData data];
    NSKeyedArchiver * archiver=[[NSKeyedArchiver alloc] initForWritingWithMutableData:data];
    [archiver encodeObject:person1 forKey:@"person1"];
    [archiver encodeObject:person2 forKey:@"person2"];
    [archiver finishEncoding];
    [data writeToFile:path atomically:YES];

    return 0;
}
```

代码分析

既然要练习存储多个对象，首先要建立至少两个对象 person1 和 person2。

然后要确定存储对象的文件的路径 path。

NSMutableData * data=[NSMutableData data];    NSMutableData 是 NSData 类型的可变类型，通过看这个类提供的方法，我们很容易看到 data 类方法，它返回的是本类的对象（实际上是一个单例方法）。

NSKeyedArchiver * archiver=[[NSKeyedArchiver alloc] initForWritingWithMutableData:data];这行代码的作用是将归档对象和 data 可变类型相连接，这样的好处就是在后边归档的时候，数据都会保存在 data 对象中（这也是为什么使用二进制类型的可变类型的原因，归档一个，就添加一部分数据）。

[archiver encodeObject:person1 forKey:@"person1"];

[archiver encodeObject:person2 forKey:@"person2"];

上边两行分别对 person1 和 person2 两个对象进行归档。

[archiver finishEncoding];这行代码是结束归档的标志，当你归档完对象之后，一定要调用一下这个方法，否则会出错。

最后将归档完成获得的 data 存入文件中。

**解档多个对象**

通过上边的代码，我们可以将多个 Person 类的实例对象保存到本地文件中，那么我们再怎么读取呢？

在 main.m 文件中编写以下代码：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {

    NSString * path=@"/Users/xieyang/Desktop/a.txt";
    NSData * theData=[NSData dataWithContentsOfFile:path];
    NSKeyedUnarchiver * unarchiver=[[NSKeyedUnarchiver alloc]initForReadingWithData:theData];

    Person * thePerson1=[unarchiver decodeObjectForKey:@"person1"];
    NSLog(@"the person1's name is :%@,sex is :%d,age is :%d",thePerson1.name,thePerson1.sex,thePerson1.age);

    Person * thePerson2=[unarchiver decodeObjectForKey:@"person2"];

    NSLog(@"the person2's name is :%@,sex is :%d,age is :%d",thePerson2.name,thePerson2.sex,thePerson2.age);

    return 0;
}
```

输出结果：**the person1's name is :ZhangSan,sex is :1,age is :10**

**       the person2's name is :LiSi,sex is :1,age is :20**

解档代码分析

1.  首先我们要知道解档文件的路径 path，而且确定这个文件中的数据是通过上面的方式归档的；
2.  由于采用上边的方式归档的是一个 NSMutableData 类型的数据，所以我们可以用 NSData 类型的对象去接。
3.  获得二进制数据之后，由于我们无法直接对二进制数据进行操作，所以还得先将数据解档才行。
4.  解档之后我们就可以通过这个 decodeObjectForKey 方法根据键来查找对应的对象

如果归档对象过多时，可以考虑将这些自定义对象保存到数组中，然后通过保存数组的方式存储过多的对象；当需要提取出这些数据时，可以将采用遍历数组的方式将自定义对象提取出来。

通过本节内容的学习之后，我们就可以对数据(包括基本数据和对象数据)进行永久的保存，同时，也能够实现对文件中的数据进行读取。