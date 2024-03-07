# Go 语言结构声明（定义）

接口是双方约定的一种合作协议。接口实现者不需要关心接口会被怎样使用，调用者也不需要关心接口的实现细节。接口是一种类型，也是一种抽象结构，不会暴露所含数据的格式、类型及结构。

## 接口声明的格式

每个接口类型由数个方法组成。接口的形式代码如下：

```
type 接口类型名 interface{
    方法名 1( 参数列表 1 ) 返回值列表 1
    方法名 2( 参数列表 2 ) 返回值列表 2
    …
}
```

对各个部分的说明：

*   接口类型名：使用 type 将接口定义为自定义的类型名。Go 语言的接口在命名时，一般会在单词后面添加 er，如有写操作的接口叫 Writer，有字符串功能的接口叫 Stringer，有关闭功能的接口叫 Closer 等。
*   方法名：当方法名首字母是大写时，且这个接口类型名首字母也是大写时，这个方法可以被接口所在的包（package）之外的代码访问。
*   参数列表、返回值列表：参数列表和返回值列表中的参数变量名可以被忽略，例如：

```
type writer interface{
    Write([]byte) error
}
```

## 开发中常见的接口及写法

Go 语言提供的很多包中都有接口，例如 io 包中提供的 Writer 接口：

```
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

这个接口可以调用 Write() 方法写入一个字节数组（[]byte），返回值告知写入字节数（n int）和可能发生的错误（err error）。

类似的，还有将一个对象以字符串形式展现的接口，只要实现了这个接口的类型，在调用 String() 方法时，都可以获得对象对应的字符串。在 fmt 包中定义如下：

```
type Stringer interface {
    String() string
}
```

Stringer 接口在 Go 语言中的使用频率非常高，功能类似于 Java 或者 C# 语言里的 ToString 的操作。

Go 语言的每个接口中的方法数量不会很多。Go 语言希望通过一个接口精准描述它自己的功能，而通过多个接口的嵌入和组合的方式将简单的接口扩展为复杂的接口。本章后面的小节中会介绍如何使用组合来扩充接口。