# Python binarytree 模块的用法

> 原文：[`www.weixueyuan.net/a/632.html`](http://www.weixueyuan.net/a/632.html)

二叉树库需要安装，推荐使用 PIP 进行安装。安装命令如下：

pip install binarytree

目前的版本是 binarytree-4.0.0。安装后，需要引入该库才可以使用，方法如下：

import binarytree

树的节点使用 binarytree.Node 类来表示。例如，如果希望创建一个值为 10 的树的节点，可以用下面的方法来实现。

```

>>> root = binarytree.Node(1)
>>> type(root)
<class 'binarytree.Node'>
>>> root.value
1
```

Node 节点包括以下有用的属性。

*   value：节点的值。
*   right：右孩子节点。
*   left：左孩子节点。

可以通过创建节点并修改节点的 left 和 right 属性来创建一棵树。

```

>>> root = binarytree.Node(1)
>>> root.left = binarytree.Node(2)
>>> root.right = binarytree.Node(3)
>>> print(root)
  1
/ \
2   3
>>> root.inorder                  # 中序遍历
[Node(2), Node(1), Node(3)]
>>> root.preorder                 # 前序遍历
[Node(1), Node(2), Node(3)]
>>> root.postorder                # 后续遍历
[Node(2), Node(3), Node(1)]
>>> root.height                   # 高度
1
>>> root.size                     # 节点个数
3
>>> root.levels                   # 广度优先遍历
[[Node(1)], [Node(2), Node(3)]]
```

下面创建一个稍微复杂一点的树。

```

>>> root = binarytree.Node(1)
>>> root.left = binarytree.Node(2)
>>> root.right = binarytree.Node(3)
>>> root.left.left = binarytree.Node(4)
>>> root.left.right = binarytree.Node(5)
>>> root.levels
[[Node(1)], [Node(2), Node(3)], [Node(4), Node(5)]]
>>> print(root)
    1
  /   \
 2     3
/ \
4  5
>>> root.height
2
>>> root.size
5
```