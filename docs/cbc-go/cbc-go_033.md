# Go 语言 map（Go 语言映射）

在业务和算法中需要使用任意类型的关联关系时，就需要使用到映射，如学号和学生的对应、名字与档案的对应等。

Go 语言提供的映射关系容器为 map，map 使用散列表（hash）实现。

#### 提示

大多数语言中映射关系容器使用两种算法：散列表和平衡树。

散列表可以简单描述为一个数组（俗称“桶”），数组的每个元素是一个列表。根据散列函数获得每个元素的特征值，将特征值作为映射的键。如果特征值重复，表示元素发生碰撞。碰撞的元素将被放在同一个特征值的列表中进行保存。散列表查找复杂度为 O(1)，和数组一致。最坏的情况为 O(n)，n 为元素总数。散列需要尽量避免元素碰撞以提高查找效率，这样就需要对“桶”进行扩容，每次扩容，元素需要重新放入桶中，较为耗时。

平衡树类似于有父子关系的一棵数据树，每个元素在放入树时，都要与一些节点进行比较。平衡树的查找复杂度始终为 O(log n)。

## 添加关联到 map 并访问关联和数据

Go 语言中 map 的定义是这样的：

map[KeyType]ValueType

*   KeyType 为键类型。
*   ValueType 是键对应的值类型。

一个 map 里，符合 KeyType 和 ValueType 的映射总是成对出现。

下面代码展示了 map 的基本使用环境。

```
scene := make(map[string]int)

scene["route"] = 66

fmt.Println(scene["route"])

v := scene["route2"]
fmt.Println(v)
```

代码输出如下：
66
0

代码说明如下：

*   第 1 行 map 是一个内部实现的类型，使用时，需要手动使用 make 创建。如果不创建使用 map 类型，会触发宕机错误。
*   第 3 行向 map 中加入映射关系。写法与使用数组一样，key 可以使用除函数以外的任意类型。
*   第 5 行查找 map 中的值。
*   第 7 行中，尝试查找一个不存在的键，那么返回的将是 ValueType 的默认值。

某些情况下，需要明确知道查询中某个键是否在 map 中存在，可以使用一种特殊的写法来实现，看下面的代码：

```
v, ok := scene["route"]
```

在默认获取键值的基础上，多取了一个变量 ok，可以判断键 route 是否存在于 map 中。

map 还有一种在声明时填充内容的方式，代码如下：

```
m := map[string]string{
    "W": "forward",
    "A": "left",
    "D": "right",
    "S": "backward",
}
```

例子中并没有使用 make，而是使用大括号进行内容定义，就像 JSON 格式一样，冒号的左边是 key，右边是值，键值对之间使用逗号分隔。