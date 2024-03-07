# OC 之 NSString

在 Foundation 框架中，给大家列举了几个常用的基本数据类型，并且在以后的不断学习中，还会不断地给大家补充一些可能会用到的接口类的使用方法。

（提示：Xcode 的功能非常强大，当你不知道这个类型怎么用的时候，可以采用“command+鼠标点击类名”的方式看看这个类中都提供了哪些可供使用的接口。）

在 OC 编程中，凡是由类声明的对象的书写的格式都是 ：类名  ＊  对象名

另外，对于类对象的初始化，也需要视情况而定。准确的做法是，当需要初始化一个类对象时，采取的方法是先去类文件中去找，看有没有类中已经写好的接口方法供我们调用（对于方法的选择，首先看方法的返回类型是不是本类的对象，然后通过方法的命名大概地猜测方法的功能），如果没有，再考虑 alloc init 的方式。从下面列举的几种基本数据类型中逐渐适应这种方式。

NSString 类型相当于 C 语言中的 char *，代表的是字符串类型。在 OC 中，字符串的表示方式和 C 语言略有不同。在 C 语言中，字符串的指示标志是“”，而在 OC 中，字符串的指示标志为@“”。

NSString 这个数据类型在 Foundation 框架中的 NSString.h 文件中，大家可以进入文件中查看。

**NSString****的创建**

在 NSString 类中提供了创建对象的几种方式：

+ (instancetype)stringWithString:(NSString *)string;

- (instancetype)initWithString:(NSString *)aString;

（提示：instancetype 和 id 的意义是一样的，但是这不用 id 的原因，是 instancetype 能够返回本类的对象，而如果使用 id 的话，就是任意类型的对象。对于这两个的使用：在类中需要返回本类对象的时候就使用 instancetype，在其他地方就使用 id）。

当运用这两个方法初始化对象时，Xcode 会提示让我们直接使用：NSString * str=@"";这种形式。可见，NSString 这个类型是可以直接赋值的，但是它还是遵循我们的原则的，必须对对象进行分配空间初始化。

+ (instancetype)stringWithFormat:(NSString *)format

这个类方法非常的强大，我们可以使用它来将一些外界的变量或常量与任意字符串进行组合，将组合成的新的字符串赋值给新声明的字符串对象，如：

```
#import <Foundation/Foundation.h>
int main() {
       int count=5;
           NSString * str=[NSString stringWithFormat:@"The count is :%d",count];
           NSLog(@"%@",str);
           return 0;
}
```

输出结果为：**The count is :5**

**获取字符串长度**

在 NSString 类中关于字符串长度，为我们提供了两种方式获得。一种是通过调用已有字符串对象的 length 属性，另一种是向这个对象发送 length 消息(就是调用 length 方法)。

```
#import <Foundation/Foundation.h>
int main() {
    int count=5;
    NSString * str=[NSString stringWithFormat:@"The count is :%d",count];
    NSLog(@"%@",str);
    NSLog(@"%ld",[str length]);//也可以是 str.length
      return 0;
}
```

输出结果为：**The count is :5**

                                  **15**

**判断字符串是否相等**

NSString 类中，提供了一个比较字符串是否相等的方法：

- (BOOL)isEqualToString:(NSString *)aString;

这个方法返回的是一个 BOOL 类型的值，可以赋值给一个整形变量，如果相等，返回 1；反之，返回 0。

```
#import <Foundation/Foundation.h>
int main() {
       NSString * str1=@"abc";
       NSString * str2=@"ABC";
       int equal=[str1 isEqualToString:str2];
       NSLog(@"%d",equal);
       return 0;
}
```

输出结果为：0

从这个小程序中，可以得到这样的信息：这个方法默认是区分大小写比较的。

**字符串比较**

除了判断是否相等以外，NSString 类还提供了两个字符串进行大小比较的方法：

- (NSComparisonResult)compare:(NSString *)string;

注意：这个方法返回的是一个枚举类型的数值，这个枚举的定义是这样的：

typedef NS_ENUM(NSInteger, NSComparisonResult)

{NSOrderedAscending = -1L, NSOrderedSame, NSOrderedDescending};

NSOrderedAscending  = -1,   左边小于右边

NSOrderedSame        = 0,   两边相等

NSOrderedDescending  =1     左边大于右边

方法使用实例：

```
#import <Foundation/Foundation.h>
int main() {
       NSString * str1=@"abc";
       NSString * str2=@"ABC";
       int equal=[str1 compare:str2];
       NSLog(@"%d",equal);
       return 0;
}
```

输出结果：1

可以看出，这个方法默认也是区分大小写的。在 NSString 类中还有更多的比较的方法（例如：可以自己选择比较的条件）。

**判断字符串内是否包含另一字符串**

NSString 类中还提供了判断字符串之间是否相符包含的方法：

- (BOOL)containsString:(NSString *)str

例子：

```
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
    NSString * str1=@"a10b9c";
    NSString * str2=@"b9c";
    int i=[str1 containsString:str2];
    NSLog(@"%d",i);
    return 0;
}
```

输出结果：1

（注意：NSString 是不可变的，但是这并不意味着你不能操作他们。不可变的意思是一旦 NSString 对象被创建了，就不能对原有字符串进行增删改的操作。除了对原有字符串操作的限制外，例如：生成新的字符串，比较字符串等操作都可以做。）

**NSMutableString****的使用**

虽然 NSString 是不可变的，但是 Foundation 框架还给我们提供了一类可变的字符串类型：NSMutableString。

NSMutbaleString 这个类中，提供了在原有字符串基础上进行增删改的方法。

在原有字符串上添加一个字符串：

- (void)appendString:(NSString *)aString;

- (void)appendFormat:(NSString *)format, ...

-(void)insertString:(NSString*)aString atIndex:(NSUInteger)loc;

Foundation 框架中有很多方法都是可以从英文角度翻译出它的大概功能的，例如，这个方法的意思是：在 loc 索引处插入 astring 字符串。(索引：简单的理解，就是对字符串进行一对一标记，从 0 开始，例如：@“abc”这个字符串，a 的索引就是 0，b 的索引是 1，c 的索引是 2)

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSMutableString * str=[NSMutableString stringWithString:@"c.net"];
    [str insertString:@"biancheng." atIndex:2];
    NSLog(@"%@",str);
    return 0;
}
```

输出结果：**c.biancheng.net**

注意：可变字符串不能直接赋值。必须给其分配空间。

在原有字符串上删除特定部分的字符串：

- (void)deleteCharactersInRange:(NSRange)range;

补充：在这里用到 NSRange 这个类型，NSRange 是 Foundation 给我们提供的创建的结构体，它的结构是这样的：

typedef struct _NSRange {

NSUInteger location;

NSUInteger length;

} NSRange;

可以看到，只含有两个无符号长整形变量，location 是当前位置，length 是长度。

在使用这个方法时，我们首先要创建一个 NSRange 的变量，将之作为参数传递进去，

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSMutableString * str=[NSMutableString stringWithString:@"c.biancheng.net"];
    NSRange range;
    range.location=2;
    range.length=9;
    [str deleteCharactersInRange:range];
    NSLog(@"%@",str);
    return 0;
}
```

输出结果为：**c..net**

在 NSString 类中和 NSMutableString 类中还有很多方法因为不常用，没有涉及到，有兴趣的朋友可以自己研究一下 NSString.h 这个类接口文件。