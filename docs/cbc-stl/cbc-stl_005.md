# C++ string 类成员函数汇总（超全）

在定义 string 类对象时，string 类自身可以管理内存，程序员不必关注内存的分配细节。

string 类提供的各种操作函数大致分为八类：构造器和析构器、大小和容量、元素存取、字 符串比较、字符串修改、字符串接合、I/O 操作以及搜索和查找。

下表列出了 string 类的所有成员函数及它们的功能。

表 1 string 类的所有成员函数

| 函数名称 | 功能 |
| 构造函数 | 产生或复制字符串 |
| 析构函数 | 销毁字符串 |
| =，assign | 赋以新值 |
| Swap | 交换两个字符串的内容 |
| + =，append( )，push_back() | 添加字符 |
| insert () | 插入字符 |
| erase() | 删除字符 |
| clear () | 移除全部字符 |
| resize () | 改变字符数量 |
| replace() | 替换字符 |
| + | 串联字符串 |
| ==，！ =，<，<=，>，>=，compare() | 比较字符串内容 |
| size()，length() | 返回字符数量 |
| max_size () | 返回字符的最大可能个数 |
| empty () | 判断字符串是否为空 |
| capacity () | 返回重新分配之前的字符容量 |
| reserve() | 保留内存以存储一定数量的字符 |
| [],at() | 存取单一字符 |
| >>，getline() | 从 stream 中读取某值 |
| << | 将值写入 stream |
| copy() | 将内容复制为一个 C - string |
| c_str() | 将内容以 C - string 形式返回 |
| data() | 将内容以字符数组形式返回 |
| substr() | 返回子字符串 |
| find() | 搜寻某子字符串或字符 |
| begin( )，end() | 提供正向迭代器支持 |
| rbegin()，rend() | 提供逆向迭代器支持 |
| get_allocator() | 返回配置器 |