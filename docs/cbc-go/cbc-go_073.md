# Go 语言使用匿名结构体解析 JSON 数据

手机拥有屏幕、电池、指纹识别等信息，将这些信息填充为 JSON 格式的数据。如果需要选择性地分离 JSON 中的数据则较为麻烦。Go 语言中的匿名结构体可以方便地完成这个操作。

首先给出完整的代码，然后再讲解每个部分。

```
package main

import (
    "encoding/json"
    "fmt"
)

// 定义手机屏幕
type Screen struct {
    Size       float32 // 屏幕尺寸
    ResX, ResY int     // 屏幕水平和垂直分辨率
}

// 定义电池
type Battery struct {
    Capacity int // 容量
}

// 生成 json 数据
func genJsonData() []byte {
    // 完整数据结构
    raw := &struct {
        Screen
        Battery
        HasTouchID bool // 序列化时添加的字段：是否有指纹识别
    }{
        // 屏幕参数
        Screen: Screen{
            Size: 5.5,
            ResX: 1920,
            ResY: 1080,
        },

        // 电池参数
        Battery: Battery{
            2910,
        },

        // 是否有指纹识别
        HasTouchID: true,
    }

    // 将数据序列化为 json
    jsonData, _ := json.Marshal(raw)

    return jsonData
}

func main() {

    // 生成一段 json 数据
    jsonData := genJsonData()

    fmt.Println(string(jsonData))

    // 只需要屏幕和指纹识别信息的结构和实例
    screenAndTouch := struct {
        Screen
        HasTouchID bool
    }{}

    // 反序列化到 screenAndTouch
    json.Unmarshal(jsonData, &screenAndTouch)

    // 输出 screenAndTouch 的详细结构
    fmt.Printf("%+v\n", screenAndTouch)

    // 只需要电池和指纹识别信息的结构和实例
    batteryAndTouch := struct {
        Battery
        HasTouchID bool
    }{}

    // 反序列化到 batteryAndTouch
    json.Unmarshal(jsonData, &batteryAndTouch)

    // 输出 screenAndTouch 的详细结构
    fmt.Printf("%+v\n", batteryAndTouch)
}
```

## 定义数据结构

首先，定义手机的各种数据结构体，如屏幕和电池，参考如下代码：

```
// 定义手机屏幕
type Screen struct {
    Size       float32  // 屏幕尺寸
    ResX, ResY int      // 屏幕水平和垂直分辨率
}

// 定义电池
type Battery struct {
    Capacity int  // 容量
}
```

上面代码定义了屏幕结构体和电池结构体，它们分别描述屏幕和电池的各种细节参数。

## 准备 JSON 数据

准备手机数据结构，填充数据，将数据序列化为 JSON 格式的字节数组，代码如下：

```
// 生成 JSON 数据
func genJsonData() []byte {
    // 完整数据结构
    raw := &struct {
        Screen
        Battery
        HasTouchID bool  // 序列化时添加的字段：是否有指纹识别
    }{
            // 屏幕参数
        Screen: Screen{
            Size: 5.5,
            ResX: 1920,
            ResY: 1080,
        },

        // 电池参数
        Battery: Battery{
            2910,
        },

        // 是否有指纹识别
        HasTouchID: true,
    }

    // 将数据序列化为 JSON
    jsonData, _ := json.Marshal(raw)

    return jsonData
}
```

代码说明如下：

*   第 4 行定义了一个匿名结构体。这个结构体内嵌了 Screen 和 Battery 结构体，同时临时加入了 HasTouchID 字段。
*   第 10 行，为刚声明的匿名结构体填充屏幕数据。
*   第 17 行，填充电池数据。
*   第 22 行，填充指纹识别字段。
*   第 26 行，使用 json.Marshal 进行 JSON 序列化，将 raw 变量序列化为 []byte 格式的 JSON 数据。

## 分离 JSON 数据

调用 genJsonData 获得 JSON 数据，将需要的字段填充到匿名结构体实例中，通过 json.Unmarshal 反序列化 JSON 数据达成分离 JSON 数据效果。代码如下：

```
func main() {

    // 生成一段 JSON 数据
    jsonData := genJsonData()

    fmt.Println(string(jsonData))

    // 只需要屏幕和指纹识别信息的结构和实例
    screenAndTouch := struct {
            Screen
            HasTouchID bool
    }{}

    // 反序列化到 screenAndTouch 中
    json.Unmarshal(jsonData, &screenAndTouch)

    // 输出 screenAndTouch 的详细结构
    fmt.Printf("%+v\n", screenAndTouch)

    // 只需要电池和指纹识别信息的结构和实例
    batteryAndTouch := struct {
            Battery
            HasTouchID bool
    }{}

    // 反序列化到 batteryAndTouch
    json.Unmarshal(jsonData, &batteryAndTouch)

    // 输出 screenAndTouch 的详细结构
    fmt.Printf("%+v\n", batteryAndTouch)
}
```

代码说明如下：

*   第 4 行，调用 genJsonData() 函数，获得 []byte 类型的 JSON 数据。
*   第 6 行，将 jsonData 的 []byte 类型的 JSON 数据转换为字符串格式并打印输出。
*   第 9 行，构造匿名结构体，填充 Screen 结构和 HasTouchID 字段，第 12 行中的 {} 表示将结构体实例化。
*   第 15 行，调用 json.Unmarshal，输入完整的 JSON 数据（jsonData），将数据按第 9 行定义的结构体格式序列化到 screenAndTouch 中。
*   第 18 行，打印输出 screenAndTouch 中的详细数据信息。
*   第 21 行，构造匿名结构体，填充 Battery 结构和 HasTouchID 字段。
*   第 27 行，调用 json.Unmarshal，输入完整的 JSON 数据（jsonData），将数据按第 21 行定义的结构体格式序列化到 batteryAndTouch 中。
*   第 30 行，打印输出 batteryAndTouch 的详细数据信息。