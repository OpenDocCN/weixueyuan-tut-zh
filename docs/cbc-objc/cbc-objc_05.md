# OC 中类和对象的概念

OC 是一门面向对象的编程语言，理解 OC，首先要理解类与对象这两个概念。

OC 中的类可以看做 C 语言中结构体的升级版。结构体是一种构造数据类型，可以包含不同的成员（变量），每个成员的数据类型可以不一样；可以通过结构体来定义结构体变量，每个变量拥有相同的性质。例如：

```
#include <stdio.h>

//定义结构体 Student
struct Student{
    //结构体包含的变量
    char *name;
    int age;
    float score;
};

//显示学生信息
void display(struct Student stu);

int main(){
    //通过结构体来定义变量
    struct Student stu1;
    //操作结构体的成员
    stu1.name = "小明";
    stu1.age = 15;
    stu1.score = 92.5;
    //显示学生信息
    display(stu1);

    return 0;
}

void display(struct Student stu){
    printf("%s 的年龄是 %d，成绩是 %f\n", stu.name, stu.age, stu.score);
}
```

运行结果：

小明的年龄是 15，成绩是 92.500000

OC 中的类也是一种构造数据类型，但是进行了一些扩展，类的成员不但可以是变量，还可以是函数；通过类定义出来的变量也有特定的称呼，叫做“对象”。例如：

```
#import <Foundation/Foundation.h>

//声明类 Student
@interface Student : NSObject  //通过@interface 关键字来声明类
//类所包含的变量
@property NSString *name;
@property int age;
@property float score;
//类所包含的函数
-(void)display;
@end  //使用@end 关键字结束类的声明

//实现在类中声明的函数
@implementation Student  //通过@implementation 关键字来实现类中的函数
-(void)display{
    NSLog(@"%@的年龄是 %d，成绩是 %f", self.name, self.age, self.score);
}
@end  //使用@end 关键字结束类中函数的实现

int main(int argc, const char * argv[]) {
    //通过类来定义变量 stu1，即创建对象 stu1
    Student *stu1 = [[Student alloc] init];  //alloc 函数为对象分配内存空间，init 函数会初始化对象
    //操作类的成员
    stu1.name = @"小明";
    stu1.age = 15;
    stu1.score = 92.5;
    //调用类的函数
    [stu1 display];

    return 0;
}
```

运行结果：

小明的年龄是 15，成绩是 92.500000

第 4~11 行代码用来声明一个类。`interface`是“接口”的意思，OC 使用`@interface`来声明一个类；`Student`是类的名称；`NSObject`表示派生自哪个类，这一点大家暂时不必急于理解。

在 OC 中，类所包含的变量和函数都有特定的称呼，变量被称为属性（Property），函数被称为方法（Method），属性和方法统称为类的成员（Member）。从上面的代码可以看出，声明类的属性要使用`@property`关键字，而声明类的方法不需要使用任何关键字。

方法

property 是“属性”的意思，OC 使用`@property`来声明类所包含的变量。在 OC 中，类所包含的变量

在 C 语言中，通过结构体名称就可以完成结构体变量的定义，并分配内存空间；但是在 Java 中，仅仅通过类来定义变量不会分配内存空间，必须使用 new 关键字来完成内存空间的分配。

可以将类比喻成图纸，对象比喻成零件，图纸说明了零件的参数及其承担的任务；一张图纸可以生产出具有相同性质的零件，不同图纸可以生产不同类型的零件。

在 Java 中，使用 new 关键字，就可以通过类来创建对象，即将图纸生产成零件，这个过程叫做类的实例化，因此也称对象是类的一个实例。

注意：类只是一张图纸，起到说明的作用，不占用内存空间；对象才是具体的零件，要有地方来存放，才会占用内存空间。

类所包含的变量和函数都有特定的称呼，变量被称为属性（通常也称成员变量），函数被称为方法，属性和方法统称为类的成员。