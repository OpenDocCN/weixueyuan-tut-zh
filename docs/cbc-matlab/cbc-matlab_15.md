# MATLAB 的通用命令

> 原文：[`c.biancheng.net/view/6627.html`](http://c.biancheng.net/view/6627.html)

MATLAB 的通用命令是指在操作软件过程中经常需要使用的命令。可以在命令行窗口的提示符`>>`后面输入命令。

在本节中，我们列出 MATLAB 通用命令常用部分及其功能。我们把这些命令分为常用管理命令、变量和工作区管理命令、命令行窗口控制、文件和工作环境管理等类型，如下表所示。

MATLAB 的通用命令

| 常用管理命令 | 相关功能 |
| help | MATLAB 函数和 M 文件的在线帮助 |
| version | MATLAB 版本号 |
| ver | 显示 MathWorks 产品的版本信息 |
| path | 控制 MATLAB 的目录搜索路径 |
| addpath | 将目录添加到 MATLAB 的搜索路径上 |
| rmpath | 从 MATLAB 的搜索路径上删除目录 |
| whatsnew | 显示 MATLAB 和工具箱的 README 文件 |
| what | 列出响应目录下的 M 文件、MAT 文件和 MEX 文件 |
| which | 函数和文件定位 |
| type | 列出文件 |
| doc | 在 help 浏览窗口中显示帮助信息 |
| lookfor | 在 help 文本中搜索关键字 |
| lasterr | 上一条出错信息 |
| error | 显示出错信息 |
| profile | 探查函数的执行时间 |
| 变量和工作区管理命令 | 相关功能 |
| who，whos | 列出内存中的变量目录 |
| disp | 显示文本或阵列 |
| clear | 从工作区中清除项目 |
| mlock | 防止 M 文件被删除 |
| munlock | 允许删除 M 文件 |
| length | 求向量或矩阵的长度 |
| size | 求阵列维大小 |
| save | 将工作区变量保存到磁盘 |
| load | 从磁盘中恢复变量 |
| pack | 释放工作区内存 |
| 命令行窗口控制 | 相关功能 |
| echo | 控制 M 文件执行过程中是否启用命令回显 |
| format | 控制输出显示格式 |
| more | 控制命令行窗口的分页显示 |
| 文件和工作环境管理 | 相关功能 |
| diary | 在磁盘文件中保存任务 |
| dir | 现实目录列表 |
| cd | 改变工作目录 |
| mkdir | 建立目录 |
| copyfile | 复制文件 |
| delete | 删除文件和图形对象 |
| edit | 编辑 M 文件 |
| inmem | 获取内存中的 M 文件名 |
| matlabroot | 获取 MATLAB 安装的根目录名 |
| fullfile | 构造文件全名 |
| fileparts | 获取文件名的组成部分 |
| tempdir | 返回系统临时工作目录名 |
| tempname | 产生临时文件的唯一文件名 |
| 启动和退出 MATLAB | 相关功能 |
| matlabrc | 启动 MATLAB 的 M 文件 |
| startup | 启动 MATLAB 的 M 文件 |
| quit | 终止（退出）MATLAB |

在 MATLAB 命令行窗口中，为了便于对输入的内容进行编辑，MATLAB 提供了一些控制光标位置和进行简单编辑的常用编辑键和组合键，掌握这些可以在输入命令的过程中起到事半功倍的效果。

下表所示为一些常用键盘按键及其作用。

命令行中的键盘按键

| 键盘按键 | 说明 | 键盘按键 | 说明 |
| ↑ | Ctrl+P，调用上一行 | Home | Ctrl+A，光标置于当前行开头 |
| ↓ | Ctrl+N，调用下一行 | End | Ctrl+E，光标置于当行末尾 |
| ← | Ctrl+B，光标左移一个字符 | Esc | Ctrl+U，清除当前输入行 |
| → | Ctrl+F，光标右移一个字符 | Del | Ctrl+D，删除光标处的字符 |
| Ctrl+← | Ctrl+L，光标左移一个单词 | Backspace | Ctrl+H，删除光标前的字符 |
| Ctrl+→ | Ctrl+R，光标右移一个单词 | Alt+Backspace | 恢复上一次删除 |

启动 MATLAB 后就可以利用命令行窗口工作了，由于 MATLAB 是一种交互式语言，输入命令即给出运算结果。