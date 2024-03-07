# OC 之字典 NSDictionary

NSDictionary，去掉 Foundation 框架的标示“NS”，翻译过来就是“字典”。这是和 C 语言中的任何数据类型都不一样的 Foundation 框架提供的一个类。它也是存储数据的一种类型。

它的使用方法和现实生活中的字典类似。例如要用字典查一个字，首先会通过这个字的拼音或者偏旁去查这个字在字典的那一页上，然后就可以在字典上找到要查的字了。

OC 中的字典也是这么用，它里边的数据是通过键值对的形式保存的，简单的理解，键就是使用的字典上的拼音或者偏旁，而值就是要找的那个字。键和值组成一对，唯一的键只能查找出唯一的值，而每一个值可能有多个键（一个字可以用拼音查到，也可以用偏旁查找），他们是多对一的关系，这就是键值对的含义。

注意：使用键值对要求键和值都必须是对象，也就是说 C 语言的基本数据类型是不能当键和值的(必须要把他们转化成对象才能用，后面会介绍具体的转化思路和代码)，一般用 OC 中的 NSString 字符串当作键。

理解了键值对的含义之后，下面就可以运用键值对来创建自己的字典了。

**创建 NSDictionary 对象**

+(instancetype)dictionaryWithObject:(ObjectType)object forKey:(KeyType <NSCopying>)key;//向字典中存储一个键值对

+(instancetype)dictionaryWithObjects:(NSArray<ObjectType> *)objects forKeys:(NSArray<KeyType <NSCopying>> *)keys;//向字典中存储多个键值对

+(instancetype)dictionaryWithObjectsAndKeys:(id)firstObject, ...

- (instancetype)initWithObjectsAndKeys:(id)firstObject, ...

上面共列举了四个初始化字典对象的方法。第一个方法只能初始化一个键值对。后面三种方法能存储多个键值对。

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSDictionary * dic1=[NSDictionary dictionaryWithObject:@"object1" forKey:@"key1"];
    NSLog(@"dic1 has :%@",dic1);

    NSDictionary * dic2=[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"object1",@"object2",@"object3", nil] forKeys:[NSArray arrayWithObjects:@"key1",@"key2",@"key3", nil]];
    NSLog(@"dic2 has :%@",dic2);

    NSDictionary * dic3=[NSDictionary dictionaryWithObjectsAndKeys:@"object1",@"key1",@"object2",@"key2",@"object3",@"key3", nil];
    NSLog(@"dic3 has :%@",dic3);

    NSDictionary * dic4=[[NSDictionary alloc] initWithObjectsAndKeys:@"object1",@"key1",@"object2",@"key2",@"object3",@"key3", nil];
    NSLog(@"dic4 has :%@",dic4);
    return 0;
}
```

输出结果：

**dic1 has :{**

**    key1 = object1;**

**}**

**dic2 has :{**

**    key1 = object1;**

**    key2 = object2;**

**    key3 = object3;**

**}**

**dic3 has :{**

**    key1 = object1;**

**    key2 = object2;**

**    key3 = object3;**

**}**

**dic4 has :{**

**    key1 = object1;**

**    key2 = object2;**

**    key3 = object3;**

**}**

**字典的常用方法**

1、提供了 count 属性和 count 方法，可以用来获取字典对象中键值对的数量(和 NSArray 中 count 的用法相同，不再举例)。

2、比较两个字典是否相等：

- (BOOL)isEqualToDictionary:(NSDictionary<KeyType, ObjectType> *)otherDictionary;

例子：

```
#import <Foundation/Foundation.h>
int main() {
NSDictionary * dic2=[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"object1",@"object2",@"object3", nil] forKeys:[NSArray arrayWithObjects:@"key1",@"key2",@"key3", nil]];

    NSDictionary * dic3=[NSDictionary dictionaryWithObjectsAndKeys:@"object1",@"key1",@"object2",@"key2",@"object3",@"key3", nil];

    int i=[dic2 isEqualToDictionary:dic3];
    NSLog(@"%d",i);
    return 0;
}
```

输出结果：1

 3、从字典中根据键找出对应的值；

```
#import <Foundation/Foundation.h>

int main() {
    NSDictionary * dic2=[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"object1",@"object2",@"object3", nil] forKeys:[NSArray arrayWithObjects:@"key1",@"key2",@"key3", nil]];
    NSString * str=[dic2 objectForKey:@"key1"];
    NSLog(@"%@",str);
    return 0;
}
```

输出结果：**object1**

 4、NSDictionary 还提供了 allKeys 和 allValues 两个属性，可以通过点语法调用这两个属性来获取字典中的所有键和所有值。这两个属性返回的都是一个数组，所以需要用数组去获取。

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSDictionary * dic2=[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"object1",@"object2",@"object3", nil] forKeys:[NSArray arrayWithObjects:@"key1",@"key2",@"key3", nil]];
    NSArray * keys=dic2.allKeys;
    NSArray * objects=dic2.allValues;

    NSLog(@"all keys are :%@",keys);
    NSLog(@"all values are :%@",objects);
    return 0;
}
```

输出结果为：

**all keys are :(**

**    key1,**

**    key3,**

**    key2**

**) all values are :(**

**    object1,**

**    object3,**

**    object2**

**)**

**NSMutableDictionary 的使用**

NSDictionary 也有可变的类型：NSMutableDictionary。用可变字典可以随意添加、删除或者修改键值对。

 1、向可变字典中添加键值对

- (void)setObject:(ObjectType)anObject forKey:(KeyType <NSCopying>)aKey;

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSMutableDictionary * dic=[[NSMutableDictionary alloc] initWithObjectsAndKeys:@"object1",@"key1",@"object2",@"key2", nil];
    [dic setObject:@"object3" forKey:@"key3"];
    NSLog(@"%@",dic);
    return 0;
}
```

输出结果：

**{**

**       key1 = object1;**

**       key2 = object2;**

**       key3 = object3;**

**}**

2、删除可变字典中的键值对

- (void)removeObjectForKey:(KeyType)aKey;

例子：

```
#import <Foundation/Foundation.h>

int main() {
    NSMutableDictionary * dic=[[NSMutableDictionary alloc] initWithObjectsAndKeys:@"object1",@"key1",@"object2",@"key2", nil];
    [dic removeObjectForKey:@"key1"];
    NSLog(@"%@",dic);
    return 0;
}
```

输出结果：

**{**

**    key2 = object2;**

**}**

除了上边给大家解释的常用方法，NSDictionary 和 NSMutableDictionary 类中加油一些方法，大家在编写代码的过程中可以进入类中自己练习。