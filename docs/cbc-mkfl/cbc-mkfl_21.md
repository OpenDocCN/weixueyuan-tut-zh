# Makefile 目标类型大汇总

> 原文：[`c.biancheng.net/view/7129.html`](http://c.biancheng.net/view/7129.html)

这个章节主要讲解的是模式规则中的目标。规则中的目标形式是多种多样的，它可以是一个或多个的文件、可以是一个伪目标，这是我们之前讲到过的，也是经常使用的。其实规则目标还可以是其他的类型，下面是对这些类型的详细的说明。

#### 强制目标

如果一个目标中没有命令或者是依赖，并且它的目标不是一个存在的文件名，在执行此规则时，目标总会被认为是最新的。就是说：这个规则一旦被执行，make 就认为它的目标已经被更新过。这样的目标在作为一个规则的依赖时，因为依赖总被认为更新过，因此作为依赖在的规则中定义的命令总会被执行。看一个例子：

```

clean:FORCE
    rm $(OBJECTS)
FORCE:
```

这个例子中，目标 "FORCE" 符合上边的条件。它作为目标 "clean" 的依赖，在执行 make 的时候，总被认为更新过。因此 "clean" 所在的规则而在被执行其所定义的那个命令总会被执行。这样的一个目标通常我们将其命名为 "FORCE"。

例子中使用 "FORCE" 目标的效果和将 "clean" 声明为伪目标的效果相同。

#### 空目标文件

空目标文件是伪目标的一个变种，此目标所在的规则执行的目的和伪目标相同——通过 make 命令行指定将其作为终极目标来执行此规则所定义的命令。和伪目标不同的是：这个目标可以是一个存在的文件，但文件的具体内容我们并不关心，通常此文件是一个空文件。

空目标文件只是用来记录上一次执行的此规则的命令的时间。在这样的规则中，命令部分都会使用 "touch" 在完成所有的命令之后来更新目标文件的时间戳，记录此规则命令的最后执行时间。make 时通过命令行将此目标作为终极目标，当前目标下如果不存在这个文件，"touch" 会在第一次执行时创建一个的文件。

通常，一个空目标文件应该存在一个或者多个依赖文件。将这个目标作为终极目标，在它所依赖的文件比它更新时，此目标所在的规则的命令行将被执行。就是说如果空目标文件的依赖文件被改变之后，空目标文件所在的规则中定义的命令会被执行。看一个例子：

```

print:foot.c bar.c
    lpr -p $?
    touch print
```

执行 "make print" ,当目标文件 "print" 的依赖文件被修改之后，命令 "lpr -p $?" 都会被执行，打印这个被修改的文件。

#### 特殊的目标

| 名称 | 功能 |
| .PHONY: | 这个目标的所有依赖被作为伪目标。伪目标是这样一个目标：当使用 make 命令行指定此目标时，这个目标所在的规则定义的命令、无论目标文件是否存在都会被无条件执行。 |
| .SUFFIXES: | 这个目标的所有依赖指出了一系列在后缀规则中需要检查的后缀名 |
| .DEFAULT: | Makefile 中，这个特殊目标所在规则定义的命令，被用在重建那些没有具体规则的目标，就是说一个文件作为某个规则的依赖，却不是另外一个规则的目标时，make 程序无法找到重建此文件的规则，这种情况就执行 ".DEFAULT" 所指定的命令。 |
| .PRECIOUS: | 这个特殊目标所在的依赖文件在 make 的过程中会被特殊处理：当命令执行的过程中断时，make 不会删除它们。而且如果目标的依赖文件是中间过程文件，同样这些文件不会被删除。 |
| .INTERMEDIATE: | 这个特殊目标的依赖文件在 make 执行时被作为中间文件对待。没有任何依赖文件的这个目标没有意义。 |
| .SECONDARY: | 这个特殊目标的依赖文件被作为中过程的文件对待。但是这些文件不会被删除。这个目标没有任何依赖文件的含义是：将所有的文件视为中间文件。 |
| .IGNORE | 这个目标的依赖文件忽略创建这个文件所执行命令的错误，给此目标指定命令是没有意义的。当此目标没有依赖文件时，将忽略所有命令执行的错误。 |
| .DELETE_ON_ERROR: | 如果在 Makefile 中存在特殊的目标 ".DELETE_ON_ERROR" ，make 在执行过程中，荣国规则的命令执行错误，将删除已经被修改的目标文件。 |
| .LOW_RESOLUTION_TIME: | 这个目标的依赖文件被 make 认为是低分辨率时间戳文件，给这个目标指定命令是没有意义的。通常的目标都是高分辨率时间戳。 |
| .SILENT: | 出现在此目标 ".SILENT" 的依赖文件列表中的文件，make 在创建这些文件时，不打印出此文件所执行的命令。同样，给目标 "SILENT" 指定命令行是没有意义的。 |
| .EXPORT_ALL_VARIABLES: | 此目标应该作为一个简单的没有依赖的目标，它的功能是将之后的所有变量传递给子 make 进程。 |
| .NOTPARALLEL: | Makefile 中如果出现这个特殊目标，则所有的命令按照串行的方式执行，即使是存在 make 的命令行参数 "-j" 。但在递归调用的子 make 进程中，命令行可以并行执行。此目标不应该有依赖文件，所有出现的依赖文件将会被忽略。 |

#### 多规则目标

Makefile 中，一个文件可以作为多个规则的目标。这种情况时，以这个文件为目标的规则的所有依赖文件将会被合并成此目标一个依赖文件列表，当其中的任何一个依赖文件比目标更新时，make 将会执行特定的命令来重建这个目标。

对于一个多规则的目标，重建这个目标的命令只能出现在一个规则中。如果多个规则同时给出重建此目标的命令，make 将使用最后一个规则中所定义的命令，同时提示错误信息。某些情况，需要对相同的目标使用不同的规则中所定义的命令，我们需要使用另一种方式——双冒号规则来实现。

一个仅仅描述依赖关系的描述规则可以用来给出一个或者时多个目标文件的依赖文件。例如，Makefile 中通常存在一个变量，就像我们以前提到的 "objects" ,它定义为所有的需要编译的生成 .o 文件的列表。这些 .o 文件在其源文件中包含的头文件 "config.h" 发生变化之后能够自动的被重建，我们可以使用多目标的方式来书写 Makefile：

```

objects=foo.o bar.o
foo.o:defs.h
bar.o:defs.h test.h
$(objects):config.h
```

这样做的好处是：我们可以在源文件增加或者删除了包含的头文件以后不用修改已存在的 Makefile 的规则，只需要增加或者删除某一个 .o 文件依赖的头文件。这种方式很简单也很方便。

我们也可以通过一个变量来增加目标的依赖文件，使用 make 的命令行来指定某一个目标的依赖头文件，例如：
extradeps=
$(objects):$(exteradeps)
它的意思是：如果我们执 "make exteradeps=foo.h" 那么 "foo.h" 将作为所有的 .o 文件的依赖文件。当然如果只执行 "make" 的话，就没有指定任何文件作为 .o 文件的依赖文件。