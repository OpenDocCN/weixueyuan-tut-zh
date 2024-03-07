# Shell 文件包含

像其他语言一样，Shell 也可以包含外部脚本，将外部脚本的内容合并到当前脚本。

Shell 中包含脚本可以使用：

```
. filename
```

或

```
source filename
```

两种方式的效果相同，简单起见，一般使用点号(.)，但是注意点号(.)和文件名中间有一空格。

例如，创建两个脚本，一个是被调用脚本 subscript.sh，内容如下：

```
url="http://see.xidian.edu.cn/cpp/view/2738.html"
```

一个是主文件 main.sh，内容如下：

```
#!/bin/bash
. ./subscript.sh
echo $url
```

执行脚本：

```
$chomd +x main.sh
./main.sh
http://see.xidian.edu.cn/cpp/view/2738.html
$
```

注意：被包含脚本不需要有执行权限。