# KVC 和 KVO

之前在对类中的属性进行调用时，往往通过点语法直接就能调用。本节学习一种间接调用对象属性的方法，称它为“KVC”(全称：Key－Value－Coding，键值编码)。

KVC 不同于点语法，它是一种间接调取对象属性的方法。它的实现方式是通过字符串来自动找到要更改的对象属性。看一个例子就懂了：

首先创建一个工程，在工程中添加一个类，例如：Person 类：

Person.h：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject
@property (nonatomic,copy)NSString * name;
@property (nonatomic,assign)int age;
@property (nonatomic,assign)int sex;
@end
```

Person.m 中没有添加代码。

如何通过键值编码的方式间接初始化一个 Person 类的对象：

main.m：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
    Person * person=[[Person alloc] init];
    [person setValue:@"ZhangSan" forKey:@"name"];
    [person setValue:[NSNumber numberWithInt:10] forKey:@"age"];
    [person setValue:[NSNumber numberWithBool:YES] forKey:@"sex"];
    NSLog(@"The person's name is :%@,and age is :%d, sex is :%d",person.name,person.age,person.sex);
    return 0;
}
```

输出结果：**The person's name is :ZhangSan,and age is :10, sex is :1**

可以看到，在对 person 对象初始化后，通过 setValue:forKey:的方式给 person 对象的 name、age、sex 属性分别赋值。此方法的使用和字典中的 setValue:forKey:方法类似，Value 和 Key 必须是对象(两个方法完全没有关系，通过深入框架，可以看到，本节中使用的 setValue:forKey:属于 NSKeyValueCoding.h 接口中提供的方法)。

在使用键值编码对声明的对象进行操作时，Key 的值要求和对象中的属性名相同，只有这样才能对对象属性设置成功，否则程序会报告找不到你设置的 key，程序出错。

和字典类似，NSKeyValueCoding.h 不仅提供了 setValue:forKey:的方法对对象中的属性进行设置，还可以通过键值编码的方式来间接地获取到对象中的方法值。可以使用 valueForKey:的方法通过 key 的值来获取对应的属性值：

所以，上个例子中的输出语句，还可以改成：

NSLog(@"The person's name is :%@,and age is :%d, sex is :%d",

[person valueForKey:@"name"],

[[person valueForKey:@"age"] intValue],

[[person valueForKey:@"sex"] boolValue]

);

(由于取出的 value 是对象，所以还需要转化一下)

除了上边介绍到的 KVC 提供的方法，NSKeyValueCoding.h 接口中还提供了一个方法：

-(void)setValuesForKeysWithDictionary:(NSDictionary<NSString *, id> *)keyedValues;

通过翻译这个方法“用字典通过 key 设置 value”，了解到这同样是一个间接给对象赋值的方法，只不过这个方法需要传入的参数是一个字典。下面，将上面的例子改写一下；

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {

    Person * person=[[Person alloc] init];

    NSMutableDictionary * dic=[[NSMutableDictionary alloc]init];

    [dic setValue:@"ZhangSan" forKey:@"name"];
    [dic setValue:[NSNumber numberWithInt:10] forKey:@"age"];
    [dic setValue:[NSNumber numberWithBool:YES] forKey:@"sex"];

    [person setValuesForKeysWithDictionary:dic];

    NSLog(@"The person's name is :%@,and age is :%d, sex is :%d",
          [person valueForKey:@"name"],
          [[person valueForKey:@"age"] intValue],
          [[person valueForKey:@"sex"] boolValue]
          );

    return 0;
}
```

**输出结果：****The person's name is :ZhangSan,and age is :10, sex is :1**

通过不同的方法，最终得到的是一个相同的结果。有两种方式可以实现间接对对象属性赋值的操纵，那么怎么做选择呢？

对于第一种方法，可以在平常方法中使用。而第二种方法，当你从外界文件中读入字典对对象属性进行赋值时，可以极大地减少代码的工作量，提高开发效率。

**学习 KVO****：**

和 KVC 长得很像的，还有一个 KVO(全称：Key-Value-Observer，键值观察(或键值监听))

虽然它和 KVC 就差一个字母，但是它们完全没有关系，本节将它们放在一起，也只是为了初学者能够更好地区分它们。

KVO 是干什么的呢？在编程过程中，有时需要对对象的某个或某些属性进行监听，通过对象属性的变化采取相应的对策。

下面来学习一下 KVO 的具体使用。通过一个例子来具体地感受一下 KVO 的强大功能

1、创建一个工程，例如 Demo；

2、在工程中，创建一个类文件，例如 Person

在 Person.h 文件：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject
@property (nonatomic,copy)NSString * name;
@property (nonatomic,assign)int age;
@end
```

在 Person.m 文件：

```
#import "Person.h"
@implementation Person
-(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSString *,id> *)change context:(void *)context{
    NSLog(@"%@",change);
}
@end
```

可以发现，在 Person.m 文件中出现了一个类似和本类毫无关系的对象方法。先不管，先看 main.m 文件中的代码：

```
#import <Foundation/Foundation.h>
#import "Person.h"
int main(int argc, const char * argv[]) {
                  Person * person=[[Person alloc] init];
        person.name=@"ZhangSan";
        person.age=10;
        [person addObserver:person forKeyPath:@"name" options:NSKeyValueObservingOptionNew|NSKeyValueObservingOptionOld context:nil];
        [person addObserver:person forKeyPath:@"age" options:NSKeyValueObservingOptionNew|NSKeyValueObservingOptionOld context:nil];
        person.name=@"LiSi";
       person.age=20;
       [person removeObserver:person forKeyPath:@"name" context:nil];
       [person removeObserver:person forKeyPath:@"age" context:nil];
       return 0;
}
```

输出结果：

**{**

**    kind = 1;**

**    new = LiSi;**

**    old = ZhangSan;**

**}**

**{**

**    kind = 1;**

**    new = 20;**

**    old = 10;**

**}**

我们来分析一下 main.m 文件中代码：

5-7 行代码：初始化了一个 Person 类的对象，并对 person 对象的属性进行了初始化。

9、10 行代码是 KVO 的重点，通过翻译，我们可以大概了解这个方法的作用，就是给 person 这个对象添加一个观察者，着个观察者就是自己。那自己观察自己什么呢？要时刻监听 name 属性值和 age 属性的变化。options 参数的设置是要你选择一但监听的这个 name 属性值发生了变化，那么你要变化之前的值还是变化之后的值或者是其他类型的值(可以选择多种，可以进入枚举中查看),context 可以用来传递数据，没有就设置为 nil 即可。

12、13 行代码：通过直接赋值的方式将 person 对象的属性的值做了改变。这一步的目的就是要判断监听者能不能监听到数据。

最后一定要移除观察者。（对移除的顺序不做要求）

当我们运行程序的时候打断点会发现，当 person 对象值发生改变的时候，程序会进入 Person.m 文件中的那个方法中执行输出语句，这是怎么回事呢？

这就是 KVO，当你设置了谁作为监听者之后，监听者所在的类就要实现 Perosn.m 中的第 5 行代码，当监听的对象发生变化时，自动调用这个方法，并将变化的数据传递给这个方法的 change 字典，通过这个字典，我们就可以获取相应的数据。

**KVO****和通知的关系**

相同点：

同属于监听，都能够传递数据。

在程序结束时，都需要移除观察者。

不同点：

KVO 比通知更厉害的是，它不仅能够实现监听，同时还能监听对象属性的改变，这是使用通知办不到的。