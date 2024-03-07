# Go 语言单向通道——通道中的单行道

Go 的通道可以在声明时约束其操作方向，如只发送或是只接收。这种被约束方向的通道被称做单向通道。

## 单向通道的声明格式

只能发送的通道类型为`chan<-`，只能接收的通道类型为`<-chan`，格式如下：

var 通道实例 chan<- 元素类型    // 只能发送通道
var 通道实例 <-chan 元素类型    // 只能接收通道

*   元素类型：通道包含的元素类型。
*   通道实例：声明的通道变量。

## 单向通道的使用例子

示例代码如下：

```
ch := make(chan int)
// 声明一个只能发送的通道类型, 并赋值为 ch
var chSendOnly chan<- int = ch
//声明一个只能接收的通道类型, 并赋值为 ch
var chRecvOnly <-chan int = ch
```

上面的例子中，chSendOnly 只能发送数据，如果尝试接收数据，将会出现如下报错：

invalid operation: <-chSendOnly (receive from send-only type chan<- int)

同理，chRecvOnly 也是不能发送的。

当然，使用 make 创建通道时，也可以创建一个只发送或只读取的通道：

```
ch := make(<-chan int)

var chReadOnly <-chan int = ch
<-chReadOnly
```

上面代码编译正常，运行也是正确的。但是，一个不能填充数据（发送）只能读取的通道是毫无意义的。

## time 包中的单向通道

time 包中的计时器会返回一个 timer 实例，代码如下：

```
timer := time.NewTimer(time.Second)
```

timer 的 Timer 类型定义如下：

```
type Timer struct {
    C <-chan Time
    r runtimeTimer
}
```

第 2 行中 C 通道的类型就是一种只能接收的单向通道。如果此处不进行通道方向约束，一旦外部向通道发送数据，将会造成其他使用到计时器的地方逻辑产生混乱。

因此，单向通道有利于代码接口的严谨性。