# OC 之 NSData

下面为和大家简单地聊一下 NSData 这个类。NSData 类，功能是操作二进制数据。

**应用领域**

 在某些情况下，需要将字符串转换成二进制进行存储或者做其他用处时，需要使用 NSData 和 NSString 之间进行互换。

 这种情况是最常见的：当从网上下载数据（例如图片）时，通常返回的是二进制数据，那么就需要通过 NSData 类生成的对象去接，然后通过 NSData 类中提供的方法进行适当的类型转换，才能算真正的获取到数据。

在开发过程中，有时需要对二进制文件进行数据的读取，这时也会用到 NSData 类。

**实际应用 **

由于网络部分还没有涉及到，所以在这里给大家一个 NSString 和 NSData 之间进行数据类型转换的例子，通过这个例子来对 NSData 这种数据类型有一个最初的了解：

例子：

```
#import <Foundation/Foundation.h>
int main() {
    NSString * str=@"http://c.biancheng.net";
    NSData * data=[str dataUsingEncoding:NSUTF8StringEncoding];
    NSLog(@"the data is :%@",data);
    NSString * strCopy=[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
    NSLog(@"the new string is :%@",strCopy);
    return 0;
}
```

输出结果：

**the data is :<68747470 3a2f2f63 2e626961 6e636865 6e672e6e 6574>**

**the new string is :http://c.biancheng.net**

代码分析：

首先进行的是将字符串转换成二进制数据：

程序开头创建了一个字符串：@http://c.biancheng.net，通过 dataUsingEncoding：就可以直接将字符串转换成二进制数据（方法中的参数默认是 NSUTF8StringEncoding）。

然后给大家演示的是将二进制数据再转换成字符串：

在 NSString 类接口文件中，可以找到这个 initWithData:对象方法，通过它，就可以直接将二进制数据转换成字符串（参数同样默认是：NSUTF8StringEncoding）。

NSData 类还有一个可变类型：NSMutableData 类，比 NSData 对象多的功能就是可以更新对象中的二进制数据（添加、更改）。这里不再举例，编程中不经常使用。会在文件操作的章节中看到它的出现。