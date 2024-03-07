# Flask run 运行参数的用法

> 原文：[`www.weixueyuan.net/a/753.html`](http://www.weixueyuan.net/a/753.html)

通过为 run() 命令设置不同的参数，可以根据实际需要运行 Flask 框架。

## 1、Debug 模式

参数 debug=True 表示是运行在 Debug 模式下，默认是 production。

假定将 app1.run() 修改为 app1.run(debug=True)，那么运行时就可以看到下面的输出：

$ python flaskDemo3.py
* Serving Flask app "first_flask_demo" (lazy loading)
* Environment: production
   WARNING: Do not use the development server in a production
        environment.
   Use a production WSGI server instead.
* Debug mode: on                    # 运行在 Debug 模式下
* Restarting with stat
* Debugger is active!
* Debugger PIN: 181-405-252
* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)

Debug 模式的一个好处就是，如果用户修改了代码，它会自动重启服务并应用最新的代码，不用手动按 Ctrl+C 组合键来停掉服务，然后再次启动服务。

## 2、监听的主机

该配置项默认是 127.0.0.1，表示仅在本机上可以访问该 Web 服务器，其他机器是不可以访问的。为了让其他机器可以访问，可以将其设定为 0.0.0.0。

app1.run(host=’0.0.0.0’)

运行后可以发现其监听的主机 IP 发生了变化：

$ python flaskDemo3.py
* Serving Flask app "first_flask_demo" (lazy loading)
* Environment: production
   WARNING: Do not use the development server in a production
        environment.
   Use a production WSGI server instead.
* Debug mode: on                    # Debug 模式
* Restarting with stat
* Debugger is active!
* Debugger PIN: 181-405-252
    # 使用 0.0.0.0
* Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)

## 3、端口号

默认的端口号是 5000，如果我们希望修改这个值，可以使用参数 port 来实现。

app1.run(port=8080)

启动后会发现输出的消息有下面的变化：

$ python flaskDemo3.py
* Serving Flask app "first_flask_demo" (lazy loading)
* Environment: production
   WARNING: Do not use the development server in a production
        environment.
   Use a production WSGI server instead.
* Debug mode: on                        # Debug 模式
* Restarting with stat
* Debugger is active!
* Debugger PIN: 181-405-252
     # 端口是 8080
* Running on http://0.0.0.0:8080/ (Press CTRL+C to quit)