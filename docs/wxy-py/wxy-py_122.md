# Flask 命令行启动（Flask 命令）

> 原文：[`www.weixueyuan.net/a/754.html`](http://www.weixueyuan.net/a/754.html)

在前面的例子中是通过 Python 文件名来启动 Flask 应用，其实质就是运行 app1.run() 命令。Flask 自己也带有一个 Flask 命令，可以完成类似的工作。

例如有一个文件 cmdlineDemo1.py，其内容如下：

```

from flask import Flask
app1 = Flask('cmd_line_demo1')
@app1.route('/')
def demo1():
    return 'Welcome to python in one'
```

现在通过设定环境变量 FLASK_APP=flaskDemo3.py 来表示将要运行的应用是哪个，然后运行 flask run 来启动该应用。

$ flask run
* Serving Flask app "cmdlineDemo1.py"
* Environment: production
   WARNING: Do not use the development server in a production
        environment.
   Use a production WSGI server instead.
* Debug mode: off
* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)

同样的问题，如何设置 Debug 模式呢？类似地，可以使用环境变量来达到目的，即设定环境变量 FLASK_DEBUG=1 就可以在 Debug 模式下运行了。

如何设定监听的主机呢？默认是 127.0.0.1，如果我们希望设定为 0.0.0.0，那么可以在命令行后面带上参数 --host=0.0.0.0。

$ flask run --host=0.0.0.0
* Serving Flask app "cmdlineDemo1.py"
* Environment: production
   WARNING: Do not use the development server in a production
        environment.
   Use a production WSGI server instead.
* Debug mode: off
* Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)

如何设定端口号呢？可以使用命令行参数 --port=8080 这种方式来设定。

$ flask run --port=8080
* Serving Flask app "cmdlineDemo1.py"
* Environment: production
   WARNING: Do not use the development server in a production
        environment.
   Use a production WSGI server instead.
* Debug mode: off
* Running on http://127.0.0.1:8080/ (Press CTRL+C to quit)

运行环境默认是 production，如何设定为 development 呢？可以通过环境变量 FLASK_ENV 来设置，即 FLASK_ENV=development。