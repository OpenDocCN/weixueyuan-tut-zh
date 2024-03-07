# OC 中数据的存储

在这一章中，学习如何将数据保存到一个本地文件中。首先，我们心里要清楚的知道 OC 中提供了哪几种数据类型可以保存到文件中？

**和文件存储相关的数据类型**

这几种数据类型包括：NSString、NSArray、NSDictionary、NSData 以及 NSNumber 和 NSDate（后边这两种可以存储到 plist 文件中，但是没有提供直接和文件操作相关的方法）。（如果要存储其它类型的数据，首先要将数据转换为这几种中的一种，然后在存储到文件中）

下面通过一个例子介绍一下将以上这些基本类型保存到一个文件中的具体流程：（拿 NSString 类型举例）：

在 main.m 文件中编写以下代码：

```
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
    NSString * path=@"/Users/xieyang/Desktop/a.txt";
    NSString * string=@"c.biancheng.net";
    NSError * error=nil;
    [string writeToFile:path atomically:YES encoding:NSUTF8StringEncoding error:&error];
    return 0;
}
```

（提示：这个程序中的 path 字符串是文件的路径，在运行程序时，首先要确保这个路径是正确的）

运行后，去这个路径下找到 a.txt 文件，会发现成功的把 string 字符串添加到文件中（无论文件中之前有什么，运行之后就只有这个字符串）

下面我们来分析一下这段程序的思路：

因为我们是将字符串保存到一个文件中，所以首先我们要获取这个文件的绝对路径。（获取一个文件的路径一个非常简单的方法：打开命行，然后把这个文件拖进去，松开鼠标，你就会获取这个文件的路径）。

获取到路径后，我们需要知道保存的对象，也就是字符串；

两项工作做完后，可以通过：-(BOOL)writeToFile:(NSString*)path

atomically:(BOOL)useAuxiliaryFile encoding:(NSStringEncoding)enc error:(NSError **)error;这个方法（NSString 类接口中的方法）将字符串保存到 path 路径中的 a.txt 文件中。

在程序中还定义了一个 NSError 类的对象，这是因为在保存方法中有一个参数，需要这个对象的地址，所以我们需要提前声明，然后将这个对象的地址传给上面的方法。

以上就是将字符串保存到本地文件的过程。对于数组，字典等同样是这个过程。下面再给大家一个数组的例子：

在 main.m 文件中编写以下代码：

```
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
    NSString * path=@"/Users/xieyang/Desktop/a.txt";
    NSArray * array=[NSArray arrayWithObjects:@"1",@"2",@"3",@"4",nil];
    [array writeToFile:path atomically:YES];
    return 0;
}
```

将路径更改为自己本机的路径后运行，就可以看到已经把数据存进去了

通过上面的两个例子，我们就可以往其他类上扩展，能够向本地文件中保存数据，还要学会从文件中取数据，下面来学习一下怎么取数据（拿上边存进去的数组为例）：

```
#import <Foundation/Foundation.h>
int main(int argc, const char * argv[]) {
    NSString * path=@"/Users/xieyang/Desktop/a.txt";
    NSArray * array=[NSArray arrayWithContentsOfFile:path];
    NSLog(@"%@",array);
    return 0;
}
```

运行结果：**(**

**    1,**

**    2,**

**    3,**

**    4**

**)**

我们可以看到，只要知道文件的路径，告诉文件中数据的类型，就可以将文件中的数据取出来。其它类型也是这样，不再做举例。