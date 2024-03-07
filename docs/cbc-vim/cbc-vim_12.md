# vi 按字移动光标命令

vi 编辑器提供 3 组命令，这 3 组命令每次可以使光标按字移动。这 3 组命令分别如下。

## 1\. w 和 W 命令

w 和 W 命令将光标右移至下一个字的字首。这两个命令的区别在于：

*   w 命令所指的字是指英文单词、标点符号和非字母字符；
*   W 命令所指的字是指两个空格之间的任何内容。

下面实例演示了两个命令的区别。

屏幕上显示如下：

printf("hello world!\n");

将光标停留在 printf 的`p`字符之上，使用 w 命令之后，光标将移到下一个字`(`之上，如下所示：

printf("hello world!\n");

让光标再次停留到 printf 的`p`字符之上，使用 W 命令之后，光标移动到下一个`w`之上，如下所示：

printf("hello world!\n");

## 2\. e 和 E 命令

如果光标起始位置处于字内，则 e 和 E 命令将把光标移到本字字尾；如果光标起始位置处于字尾，则 e 和 E 命令将把光标移动到下一个字的字尾。

e 和 E 命令的区别在于：

*   e 命令所指的字是指英文单词、标点符号和非字母字符；
*   E 命令所指的字是指两个空格之间的任何内容。

下面实例演示了两个命令的区别。

屏幕上显示如下：

printf("hello real-world!\n");

将光标停留在 real 的字符`a`之上，使用 e 命令之后，把光标移到字尾`l`之上，如下所示：

printf("hello real-world!\n");

让光标再次停留到 real 的字符`a`之上，使用 E 命令后，光标将移动到字尾的`;`之上：

printf("hello real-world!\n");

## 3\. b 和 B 命令

如果光标处于所在字内，则 b 和 B 命令将把光标移至本字字首；如果光标处于所在字字首，则 b 和 B 命令将把光标移到上一个字的字首。两个命令的区别在于：

*   b 命令所指的字是指英文单词、标点符号和非字母字符；
*   B 命令所指的字是指两个空格之间的任何内容。

下面实例演示了两个命令的区别。

屏幕上显示如下：

printf("hello real-world!\n");

将光标停留在 world 的字符`o`之上，使用 b 命令之后，光标将移到字首的`w`之上，如下所示：

printf("hello real-world!\n");

让光标再次停留到 world 的字符`o`之上，使用 B 命令之后，光标将移动到字首的`r`之上：

printf("hello real-world!\n");