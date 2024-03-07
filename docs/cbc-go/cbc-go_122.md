# go install 命令——编译并安装

go install 的功能和 go build 类似，附加参数绝大多数都可以与 go build 通用。go install 只是将编译的中间文件放在 GOPATH 的 pkg 目录下，以及固定地将编译结果放在 GOPATH 的 bin 目录下。

本小节需要用到的代码位置是`./src/chapter11/goinstall`。

> 本套教程所有源码下载地址：[`pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ`](https://pan.baidu.com/s/1ORFVTOLEYYqDhRzeq0zIiQ)    提取密码：hfyf

使用 go install 来执行代码，参考下面的 shell：

$ export GOPATH=/home/davy/golangbook/code
$ go install chapter11/goinstall

编译完成后的目录结构如下：

```
.
├── bin
│   └── goinstall
├── pkg
│   └── linux_amd64
│       └── chapter11
│           └── goinstall
│               └── mypkg.a
└── src
    └── chapter11
        ├── gobuild
        │   ├── lib.go
        │   └── main.go
        └── goinstall
            ├── main.go
            └── mypkg
                └── mypkg.go
```

go install 的编译过程有如下规律：

*   go install 是建立在 GOPATH 上的，无法在独立的目录里使用 go install。
*   GOPATH 下的 bin 目录放置的是使用 go install 生成的可执行文件，可执行文件的名称来自于编译时的包名。
*   go install 输出目录始终为 GOPATH 下的 bin 目录，无法使用`-o`附加参数进行自定义。
*   GOPATH 下的 pkg 目录放置的是编译期间的中间文件。