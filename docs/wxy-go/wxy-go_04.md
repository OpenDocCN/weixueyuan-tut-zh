# Mac OS 系统下安装和配置 Go 语言

> 原文：[`www.weixueyuan.net/a/393.html`](http://www.weixueyuan.net/a/393.html)

在 Mac OS 系统下安装 Go 语言，首先需要在 Go 语言官网（[`golang.google.cn/dl/`](https://golang.google.cn/dl/)）下载适合 Mac OS 系统的格式为 PKG 的安装程序。下载完成后双击启动安装，按照提示，在“/usr/local/go”中安装 Go 语言，并且将文件夹“/usr/local/go/bin”添加到 PATH 环境变量中。详细的操作步骤如下所示：

## 1、Mac OS 系统下安装 Go 语言

双击下载好的格式为 PKG 的 Go 语言安装程序，按照指引，即可安装成功。在命令行中输入 `go version` 后，如果能够获取到 Go 语言的版本号，则代表安装成功。

## 2、Mac OS 系统下配置环境变量

1) 打开终端输入 `cd ~` 进入用户主目录。

2) 输入 `ls -all` 命令查看是否存在 .bash_profile。

3) 使用 `vim .bash_profile` 打开文件。

4) 输入 `i` 进入 vim 编辑模式。

5) 输入下面代码，其中 GOPATH 是日常开发的根目录。GOBIN 是 GOPATH 下的 bin 目录。

export GOPATH=/Users/steven/Documents/go_project
export GOROOT= /Usr/local/go
export GOBIN=$GOROOT/bin
export PATH=$PATH:$GOBIN

6) 按 esc 键，并输入 `wq` 保存并退出编辑，可以再次输入 `vim .bash_profile` 查看是否保存成功。

7) 输入 `source ~/.bash_profile` 完成对 Go 语言环境变量的配置，注意，配置成功没有提示。

8) 输入 `go env` 查看配置结果。