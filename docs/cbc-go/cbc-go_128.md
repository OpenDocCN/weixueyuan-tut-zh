# Go 语言反射——性能和灵活性的双刃剑

现在的一些流行设计思想需要建立在反射基础上，如控制反转（Inversion Of Control，IOC）和依赖注入（Dependency Injection，DI）。Go 语言中非常有名的 Web 框架 martini（https://github.com/go-martini/martini）就是通过依赖注入技术进行中间件的实现，例如使用 martini 框架搭建的 http 的服务器如下：

```
package main

import "github.com/go-martini/martini"

func main() {
    m := martini.Classic()
    m.Get("/", func() string {
        return "Hello world!"
    })
    m.Run()
}
```

第 7 行，响应路径`/`的代码使用一个闭包实现。如果希望获得 Go 语言中提供的请求和响应接口，可以直接修改为：

```
m.Get("/", func(res http.ResponseWriter, req *http.Request) string {
    // 响应处理代码……
})
```

martini 的底层会自动通过识别 Get 获得的闭包参数情况，通过动态反射调用这个函数并传入需要的参数。martini 的设计广受好评，但同时也有人指出，其运行效率较低。其中最主要的因素是大量使用了反射。

虽然一般情况下，I/O 的延迟远远大于反射代码所造成的延迟。但是，更低的响应速度和更低的 CPU 占用依然是 Web 服务器追求的目标。因此，反射在带来灵活性的同时，也带上了性能低下的桎梏。

要用好反射这把双刃剑，就需要详细了解反射的性能。下面的一些基准测试从多方面对比了原生调用和反射调用的区别。

## 1) 结构体成员赋值对比

反射经常被使用在结构体上，因此结构体的成员访问性能就成为了关注的重点。下面例子中使用一个被实例化的结构体，访问它的成员，然后使用 Go 语言的基准化测试可以迅速测试出结果。

反射性能测试的完整代码位于`./src/chapter12/reflecttest/reflect_test.go`，下面是对各个部分的详细说明。

> 本套教程所有源码下载地址：[`pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ`](https://pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ)    提取密码：hfyf

原生结构体的赋值过程：

```
// 声明一个结构体, 拥有一个字段
type data struct {
    Hp int
}

func BenchmarkNativeAssign(b *testing.B) {

    // 实例化结构体
    v := data{Hp: 2}

    // 停止基准测试的计时器
    b.StopTimer()
    // 重置基准测试计时器数据
    b.ResetTimer()

    // 重新启动基准测试计时器
    b.StartTimer()

    // 根据基准测试数据进行循环测试
    for i := 0; i < b.N; i++ {

        // 结构体成员赋值测试
        v.Hp = 3
    }

}
```

代码说明如下：

*   第 2 行，声明一个普通结构体，拥有一个成员变量。
*   第 6 行，使用基准化测试的入口。
*   第 9 行，实例化 data 结构体，并给 Hp 成员赋值。
*   第 12～17 行，由于测试的重点必须放在赋值上，因此需要极大程度地降低其他代码的干扰，于是在赋值完成后，将基准测试的计时器复位并重新开始。
*   第 20 行，将基准测试提供的测试数量用于循环中。
*   第 23 行，测试的核心代码：结构体赋值。

接下来的代码分析使用反射访问结构体成员并赋值的过程。

```
func BenchmarkReflectAssign(b *testing.B) {

    v := data{Hp: 2}

    // 取出结构体指针的反射值对象并取其元素
    vv := reflect.ValueOf(&v).Elem()

    // 根据名字取结构体成员
    f := vv.FieldByName("Hp")

    b.StopTimer()
    b.ResetTimer()
    b.StartTimer()

    for i := 0; i < b.N; i++ {

        // 反射测试设置成员值性能
        f.SetInt(3)
    }
}
```

代码说明如下：

*   第 6 行，取 v 的地址并转为反射值对象。此时值对象里的类型为 *data，使用值的 Elem() 方法取元素，获得 data 的反射值对象。
*   第 9 行，使用 FieldByName() 根据名字取出成员的反射值对象。
*   第 11～13 行，重置基准测试计时器。
*   第 18 行，使用反射值对象的 SetInt() 方法，给 data 结构的`Hp`字段设置数值 3。

这段代码中使用了反射值对象的 SetInt() 方法，这个方法的源码如下：

```
func (v Value) SetInt(x int64) {
    v.mustBeAssignable()
    switch k := v.kind(); k {
    default:
        panic(&ValueError{"reflect.Value.SetInt", v.kind()})
    case Int:
        *(*int)(v.ptr) = int(x)
    case Int8:
        *(*int8)(v.ptr) = int8(x)
    case Int16:
        *(*int16)(v.ptr) = int16(x)
    case Int32:
        *(*int32)(v.ptr) = int32(x)
    case Int64:
        *(*int64)(v.ptr) = x
    }
}
```

可以发现，整个设置过程都是指针转换及赋值，没有遍历及内存操作等相对耗时的算法。

## 2) 结构体成员搜索并赋值对比

```
func BenchmarkReflectFindFieldAndAssign(b *testing.B) {

    v := data{Hp: 2}

    vv := reflect.ValueOf(&v).Elem()

    b.StopTimer()
    b.ResetTimer()
    b.StartTimer()

    for i := 0; i < b.N; i++ {

        // 测试结构体成员的查找和设置成员的性能
        vv.FieldByName("Hp").SetInt(3)
    }

}
```

这段代码将反射值对象的 FieldByName() 方法与 SetInt() 方法放在循环里进行检测，主要对比测试 FieldByName() 方法对性能的影响。FieldByName() 方法源码如下：

```
func (v Value) FieldByName(name string) Value {
    v.mustBe(Struct)
    if f, ok := v.typ.FieldByName(name); ok {
        return v.FieldByIndex(f.Index)
    }
    return Value{}
}
```

底层代码说明如下：

*   第 3 行，通过名字查询类型对象，这里有一次遍历过程。
*   第 4 行，找到类型对象后，使用 FieldByIndex() 继续在值中查找，这里又是一次遍历。

经过底层代码分析得出，随着结构体字段数量和相对位置的变化，FieldByName() 方法比较严重的低效率问题。

## 3) 调用函数对比

反射的函数调用，也是使用反射中容易忽视的性能点，下面展示对普通函数的调用过程。

```
// 一个普通函数
func foo(v int) {

}

func BenchmarkNativeCall(b *testing.B) {

    for i := 0; i < b.N; i++ {
        // 原生函数调用
        foo(0)
    }
}

func BenchmarkReflectCall(b *testing.B) {

    // 取函数的反射值对象
    v := reflect.ValueOf(foo)

    b.StopTimer()
    b.ResetTimer()
    b.StartTimer()

    for i := 0; i < b.N; i++ {
        // 反射调用函数
        v.Call([]reflect.Value{reflect.ValueOf(2)})
    }
}
```

代码说明如下：

*   第 2 行，一个普通的只有一个参数的函数。
*   第 10 行，对原生函数调用的性能测试。
*   第 17 行，根据函数名取出反射值对象。
*   第 25 行，使用 reflect.ValueOf(2) 将 2 构造为反射值对象，因为反射函数调用的参数必须全是反射值对象，再使用 []reflect.Value 构造多个参数列表传给反射值对象的 Call() 方法进行调用。

反射函数调用的参数构造过程非常复杂，构建很多对象会造成很大的内存回收负担。Call() 方法内部就更为复杂，需要将参数列表的每个值从 reflect.Value 类型转换为内存。调用完毕后，还要将函数返回值重新转换为 reflect.Value 类型返回。因此，反射调用函数的性能堪忧。

## 4) 基准测试结果对比

测试结果如下：

```
$ go test -v -bench=.
goos: linux
goarch: amd64
BenchmarkNativeAssign-4                        2000000000               0.32 ns/op
BenchmarkReflectAssign-4                       300000000               4.42 ns/op
BenchmarkReflectFindFieldAndAssign-4           20000000               91.6 ns/op
BenchmarkNativeCall-4                          2000000000               0.33 ns/op
BenchmarkReflectCall-4                         10000000               163 ns/op
PASS
```

结果分析如下：

*   第 4 行，原生的结构体成员赋值，每一步操作耗时 0.32 纳秒，这是参考基准。
*   第 5 行，使用反射的结构体成员赋值，操作耗时 4.42 纳秒，比原生赋值多消耗 13 倍的性能。
*   第 6 行，反射查找结构体成员且反射赋值，操作耗时 91.6 纳秒，扣除反射结构体成员赋值的 4.42 纳秒还富余，性能大概是原生的 272 倍。这个测试结果与代码分析结果很接近。SetInt 的性能可以接受，但 FieldByName() 的性能就非常低。
*   第 7 行，原生函数调用，性能与原生访问结构体成员接近。
*   第 8 行，反射函数调用，性能差到“爆棚”，花费了 163 纳秒，操作耗时比原生多消耗 494 倍。

经过基准测试结果的数值分析及对比，最终得出以下结论：

*   能使用原生代码时，尽量避免反射操作。
*   提前缓冲反射值对象，对性能有很大的帮助。
*   避免反射函数调用，实在需要调用时，先提前缓冲函数参数列表，并且尽量少地使用返回值。