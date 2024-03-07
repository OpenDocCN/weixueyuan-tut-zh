# 嵌套执行 make 的案例

> 原文：[`c.biancheng.net/view/7157.html`](http://c.biancheng.net/view/7157.html)

Makefile 中 "嵌套执行 make" 大部分是在大的工程项目中使用的，那么我们就通过一个大的项目工程来详细的分析一下如何嵌套执行 make。

假设有一个 MP3 player 的应用程序，它可以被划分为若干个组件：用户界面（ui）、编解码器（codec）以及数据管理库（db）。它们分别可以用三个程序库来表示：libui.a、libcodec.a 和 libdb.a。将这些组件紧凑的放到一起就可以组成这个应用程序。具体的文件结构展示为（我们展示的只是目录文件，没有展示详细的源文件）:

├──Makefile         //最外层的 Makefile 文件，不是目录文件。
├──include          //编译的时候需要链接的库文件
│      ├──codec   //libui.a 库文件所在的目录
│      ├──db        //libdb.a 库文件所在的目录
│      ├──ui         //libui.a 库文件所在的目录
├──lib                   //源文件所在的目录，子目录文件中包含 Makefile 文件
│      ├──codec     //编解码器所在的源文件的目录
│      ├──db           //数据库源文件所在的目录
│      ├──ui            //用户界面源文件所在目录
├──app
│      ├──player    
└──doc              //这个工程编译说明    

我们可以看到最外层有一个 Makefile 文件，这就是我们的 "总控 Makefile" 文件，我们使用这个 Makefile 调用项目中各个子目录的 Makefile 文件的运行。假设只有我们的 lib 目录下和 app 目录下的各个子目录含有 Makefile 文件。那我们总控的 Makefile 的文件可以这样来写：

```

lib_codec := lib/codec
lib_db    := lib/db
lib_ui     := lib/ui
libraries   := $(lib_codec) $(lib_db) $(lib_ui)
player    := app/player

.PHONY : all $(player) $(libraries)
all : $(player)

$(player) $(libraries) :
    $(MAKE) -C $@
```

我们可以看到在 "总控 Makefile" 中，一个规则在工作目标上列出了所有的子目录，它对每一个子目录的 Makefile 调用的代码是：

$(player) $(libraries) :
      $(MAKE) -C $@

在 Makefile 文件中，MAKE 变量应该总是用来调用 make 程序。make 程序一看到 MAKE 变量就会把它设成 make 的实际路径，所以递归调用中的每次调用都会使用同一个执行文件。此外，当命令 --touch(-t)、--just-print(-n) 和 --question(-q) 被使用时，包含 MAKE 变量的每一行都会受到特别的处理。

由于这些“工作目标目录”被设成 .PHONY 的依赖文件，所以即使工作目标已经更新，此规则仍旧会进行更新动作。使 --directory(-C) 选项的目的是要让 make 在读取 Makefile 之前先切换到相应的 "工作目录" 。

当 make 在建立依存图的时候找不到程序库与 app/player 工作目标之间的依存关系时，这意味着建立任何程序库之前，make 将会先执行 app/player 目录中的 Makefile。显然这将会导致失败的结果，因为应用程序的链接需要程序库。为解决这个问题，我们会提供额外的依存信息：

$(player) : $(libraries)
$(lib_ui) : $(lib_db) $(lib_codec)

我们在此处做了如下的描述：运行 app/player 目录中的 Makefile 之前必须先运行程序库子目录中的 Makefile。此外，编译 lib/ui 目录中的程序代码之前必须先编译 lib/db 和 lib/codec 目录中的程序库。这么做可以确保任何自动产生的程序代码，在 lib/ui 目录中的程序代码被编译之前就已经产生出来了。

更新必要条件的时候，会引发微妙的次序问题。如同所有的依存关系，更新的次序取决于依存图的分析结果，但是当工作目标的必要条件（依赖文件）出现在同一行时，GNU make 将会从左至右的次序进行更新。例如：

all : a b c
all : d e f

如果不存在其他的依存关系，这 6 个必要条件的更新动作可以是任何次序，不过 GNU make 将会以从左向右的次序来更新出现在同一行的必要条件，这会产生如下的更新次序："a b c d e f" 或 "d e f a b c"。

注意：不要因为之前这么做更新的次序是对的，就以为每次这么做都是对的，而忘了提供完整的依存信息。

最后，依存分析可能会产生不同的次序而引发一些问题。所以，如果有一组工作目标需要以特定的次序进行更新时，就必须提供适当的必要条件来实现正确的次序。

当我们在最外层执行 make 的时候我们会看到 l 输出的信息：

make -C lib/db
make[1]: Entering directory ‘/MP3_player/lib/db’
make[1]:Update db library...
make[1]: Leaving directory ‘/MP3_player/lib/db’
make -C lib/codec
make[1]: Entering directory ‘/MP3_player/lib/codec’
make[1]:Update codec library...
make[1]: Leaving directory ‘/MP3_player/lib/codec’
make -C lib/ui
make[1]: Entering directory ‘/MP3_player/lib/ui’
make[1]:Update ui library...
make[1]: Leaving directory ‘/MP3_player/lib/ui’
make -C app/player
make[1]: Entering directory ‘/MP3_player/app/player’
make[1]:Update player library...
make[1]: Leaving directory ‘/MP3_player/app/player’

当 make 发觉它正在递归调用另一个 make 时，他会启 用--print-directory(-w) 选项，这会使得 make 输出 Entering directory(进入目录) 和 Leaving directory(离开目录) 的信息。当 --directory(-C) 选项被使用时，也会启用这个选项。我们还可以看到每一行中，MAKELEVEL 这个 make 变量的值加上方括号之后被一起输出。在这个简单的例子里，每个组件的 Makefile 只会输出组件正在更新的信息，而不会真正的更新组件。

我们通过这个例子应该可以了解，在 make 的嵌套执行执行的时候的调用子目录的方式，还有子目录再去执行 make 时候的顺序。这是一个很典型的例子，我们的每一个工程文件都可以用上面的结构展示出来，我们只要懂得每一个子目录在被调用时候的顺序，我们就可以很轻松的编写 "总控 Makefile" 。
猛击这里下载附件

 注意：附件中的的工程只能实现出我们案例中的现象，而并不是一个完整的 MP3_player 的应用程序。如果想要实现真正的音频播放功能，我们可以下载 mplayer 源码包，链接：http://www.mplayerhq.hu/MPlayer/releases/