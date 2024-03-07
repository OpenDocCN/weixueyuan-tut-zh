# Python yaml 模块：处理 yaml 文件

> 原文：[`www.weixueyuan.net/a/704.html`](http://www.weixueyuan.net/a/704.html)

yaml 的全称是 Yet Another Markup Language，顾名思义，其是计划提供一个简化版的 xml 数据格式。的确，yaml 文件要比 xml 文件简单很多。

yaml 文件有如下特点：

*   大小写敏感。
*   使用缩进表示层级关系。
*   缩进时不允许使用 Tab 键，只允许使用空格。
*   缩进的空格数目不重要，只要相同层级的元素左侧对齐即可。
*   支持行注释，以 # 表示注释的开始。

yaml 支持以下 3 种数据类型：
1) 对象，对应到 Python 中的字典。下面是一个例子：

0: a          # 0 为键，a 为值
1: b          # 字典的第二个元素

2) 数组，对应到 Python 的列表。

- 1            # -开头表示是数组元素，这是第一个元素
- 2            # 第二个元素
- 3            # 第三个元素

3) 标量，对应到 Python 的字符串、整数、浮点数等。

可以将 3 种数据类型组合起来表示复杂的数据结构。例如对于下面这个复杂的 Python 对象：

```

{
    1: [
        1,
        2,
        3,
        {
            "name": "python",
            "age": 20.0
        }
    ],
    2: "value 2"
}
```

可以用 yaml 表示为：

```

1:
- 1
- 2
- 3
- age: 20.0
  name: python
2: value 2
```

本节将介绍一种可以解析和生成 yaml 文件的库，即 pyyaml。该库需要自己安装，安装方法是：

pip install pyyaml

使用该库时，需要将其引入，方法如下：

import yaml

该库主要提供两个功能，一个是将 yaml 文件转化成 Python 对象，一个是将 Python 对象写入到 yaml 文件中。

yaml 模块提供了一个函数 load()，该函数从文件中读入内容并返回一个 Python 对象。

下面是一个简单的例子：

```

import yaml
fd = open("yamlWriteDemo1.yaml", 'r', encoding='utf-8')
data = yaml.load(fd, Loader=yaml.FullLoader)    # 读入文件内容并返回对象
fd.close()
print(data)
```

运行该脚本的输出如下：

$ cat yamlWriteDemo1.yaml          # 查看输入文件内容
0: a                               # 文件内容
1: b
$ python yamlReadDemo1.py          # 运行脚本
{0: 'a', 1: 'b'}                   # 得到的 Python 对象

写入文件可以使用 dump() 函数，该函数接收一个 Python 对象和文件对象，并将 Python 对象按照 yaml 的格式写入到文件对象中。下面是一个简单的写入 yaml 文件的例子：

```

import yaml
fd = open("yamlWriteDemo4.yaml", 'w', encoding='utf-8')
data = {                                 # 写入的对象
    1: [
        1,
        2,
        3,
        {
            "name": "python",
            "age": 20.0
        }
    ],
    2: "value 2"
}
yaml.dump(data, fd)                      # 写入 Python 对象到文件对象中
fd.close()                               # 关闭文件
```

运行后的输入如下：

$ python yamlWriteDemo4.py             # 运行脚本
$ cat yamlWriteDemo4.yaml            # 查看输出的 yaml 文件
  1:
  - 1
  - 2
  - 3
  - age: 20.0
    name: python
  2: value 2