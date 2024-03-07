# vi 屏幕滚动命令（滚屏命令）

如果文件太大，一个屏幕不能将其内容完全显示出来，vi 编辑器会采用分屏显示的方法。使用屏幕命令可以以屏幕为单位移动光标，方便地完成文件的滚屏和分页。

需要注意的是，屏幕命令不是光标移动命令，不能作为文本限定符用于删除命令中。在命令模式下和文本输入模式下均可以使用滚屏和分页命令。滚屏和分页命令如下表所示。

表：滚屏和分页命令

| 滚屏和分页命令 | 命令意义 |
| Ctrl+u | 将屏幕向前（文件头方向）翻滚半屏 |
| Ctrl+d | 将屏幕向后（文件尾方向）翻滚半屏 |
| Ctrl+f | 将屏幕向文件尾方向翻滚一整屏 |
| Ctrl+b | 将屏幕向文件首方向翻滚一整屏 |

可以在滚屏和分页命令之前加上一个数字 n，表示屏幕向前或向后翻滚 n 行，或者屏幕向前或向后移动 n 页。这些设置会被 vi 编辑器记住，下次用户在使用滚屏和分页命令时，还会滚动和上次设置相同的行数或者页数。