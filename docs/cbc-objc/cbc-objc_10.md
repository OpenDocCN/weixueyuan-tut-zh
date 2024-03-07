# OC 中的点语法

**点语法的功能**

点语法是通过“类对象.属性”的方式来调用类中属性的一种方法（不唯一，KVC 也可以，后边会提到）。

通过点语法，我们可以轻易的获取某个类对象中某个属性的值，或者为这个属性赋值。前提是这个属性能被调用。

例如，在 Person 类中声明了一个字符串类型的 name 属性（不是成员变量），可以采用以下方式调用 name 属性：

```
Person * person=[[Person alloc] init];//创建一个 Person 类的对象
person.name=@"ZhangSan";//调用 person 对象的 name 属性
```

**点语法的底层实现**

如果想弄清楚点语法的具体实现，就需要从类中创建属性的过程开始。

一个类创建属性，有两种方式。

**方式一：**

创建一个 Person 类，编写以下代码：

在 Person.h 文件中：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject{
//编写习惯，一般声明成员变量的时候名字前面加“_”，同时也是为了和下边声明的 name 相对应。
NSString * _name;
}
@property NSString * name;
@end
```

在 Person.m 文件中：

```
#import "Person.h"
@implementation Person
@synthesize name=_name;
@end

```

首先我们在.h 文件创建了一个 _name 成员变量，它是受保护的（也就是不能在外界调用）。

然后我们在第 6 行创建了一个 name 属性。通过这行代码，OC 底层隐式地创建了两个方法：

一个是 set 方法，

-(void)setName:(NSString *)name;

一个是 get 方法：

-(NSString *)name;

（这两个方法虽然你看不见，但是它的确存在）

在.m 文件中，只有一行代码：

@synthesize name=_name;

它的作用有两个：

一：将我们在.h 文件中声明的 _name 和 name 进行绑定（简单的理解，_name 和 name 都是指 name 属性）。

二：对在.h 文件声明的 get 方法和 set 方法做实现。具体地实现是这样的：

set 方法：（用于给属性赋值）

```
-(void)setName:(NSString *)name{
    _name=name;
}
```

get 方法：（用于获取属性的值）

```
-(NSString *)name{
    return _name;
}
```

通过前面的学习，在.h 文件中的方法是可以被外界调用，所以这两个方法可以被本类的对象调用。

通过调用这两个方法，我们就能对 name 这个属性进行赋值和获取值的操作。所以我们也可以叫它“方法属性”。（也可以叫“属性方法”）

**方式二：**

在.h 文件中：

```
#import <Foundation/Foundation.h>
@interface Person : NSObject
@property NSString * name;
@end
```

.m 文件中没有代码

和前者对比，我们看到少了 _name 成员变量的声明和.m 文件中的代码。但是，底层实现和前者是一样的，一个属性隐式生成带有“_”的成员变量，同时声明并实现了属性的 set 和 get 方法。

（注意：两种方式虽然这样说起来好像是一样。但是还是有区别的：使用前者创建的 set 和 get 方法，可以同时被改写；而后者在这个类中只能改写一个，如果改写两个方法，程序会报错，提示找不到 _name（原因就是没有将 _name 和 name 进行绑定）。可见，隐式创建的毕竟是他人给实现，不靠谱啊）。

**对点语法的理解**

点语法，简单的理解，就是通过“.”的方式通过调用类中的 set 或 get 方法操作属性。

下面我们来看看点语法的具体应用。

people.name=@"ZhangSan";

解释：people 是一个类的实例化对象，name 是这个类提供的方法属性。而两者中间的点就是点语法中的点。而这行代码实现的功能是：给 people 这个对象所具有的 name 方法属性进行赋值。

这行代码通过点语法调用的是类中的 set 方法，所以它还可以写成：

[people setName:@"ZhangSan"];

同样，get 方法也可以通过“.”的形式调用，例如，还是调用 people 对象的 name 属性。

NSString * theName=people.name;

这段代码实现的功能时将 people 对象的 name 属性赋值给 theName 字符串。我们把它转化一下：

NSString * theName=[people getName];

**可能会有的疑惑：**

1、有读者可能要问了，怎么才能知道调用的是哪个方法呢？其实有这样一个规律可循：

      在没有等号的情况下，默认是 get 方法。

      在有等号的情况下，我们就要分两种情况：

      在等号左边是 set 方法。

      在等号右边是 get 方法。

 2、有读者可能有这个疑惑，对于两种创建方法属性的方式不知道选择哪个？

      一般在实际开发过程中，我们可能只对 get 方法重写的时候比较多：

      -(NSString *)name{

      //添加一些代码

           return _name;

      }

      大多数情况下，对于现在编程，我们选简单的那种；如果在编程过程中需要对 set 和 get 方法都进行重写，我们就在原有基础上改为第一种就可以了。