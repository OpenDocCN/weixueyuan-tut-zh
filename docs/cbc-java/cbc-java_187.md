# Java TCP 通信：Java ServerSocket 类和 Socket 类

TCP 网络程序是指利用 Socket 编写的通信程序。利用 TCP 协议进行通信的两个应用程序是有主次之分的，一个是服务器程序，一个是客户端程序，两者的功能和编写方法不太一样。其中 ServerSocket 类表示 Socket 服务器端，Socket 类表示 Socket 客户端，两者之间的交互过程如下：

1.  服务器端创建一个 ServerSocket（服务器端套接字），调用 accept() 方法等待客户端来连接。
2.  客户端程序创建一个 Socket，请求与服务器建立连接。
3.  服务器接收客户的连接请求，同时创建一个新的 Socket 与客户建立连接，服务器继续等待新的请求。

## ServerSocket 类

ServerSocket 类是与 Socket 类相对应的用于表示通信双方中的服务器端，用于在服务器上开一个端口，被动地等待数据（使用 accept() 方法）并建立连接进行数据交互。

服务器套接字一次可以与一个套接字连接，如果多台客户端同时提出连接请求，服务器套接字会将请求连接的客户端存入队列中，然后从中取出一个套接字与服务器新建的套接字连接起来。若请求连接大于最大容纳数，则多出的连接请求被拒绝；默认的队列大小是 50。

下面简单介绍一下 ServerSocket 的构造方法和常用方法。

#### ServerSocket 的构造方法

ServerSocket 的构造方法如下所示。

*   ServerSocket()：无参构造方法。
*   ServerSocket(int port)：创建绑定到特定端口的服务器套接字。
*   ServerSocket(int port,int backlog)：使用指定的 backlog 创建服务器套接字并将其绑定到指定的本地端口。
*   ServerSocket(int port,int backlog,InetAddress bindAddr)：使用指定的端口、监听 backlog 和要绑定到本地的 IP 地址创建服务器。

在上述方法的参数中 port 指的是本地 TCP 端口，backlog 指的是监听 backlog，bindAddr 指的是要将服务器绑定到的 InetAddress。

创建 ServerSocket 时可能会拋出 IOException 异常，所以要进行异常捕捉。如下所示为使用 8111 端口的 ServerSocket 实例代码。

```
try
{
    ServerSocket serverSocket=new ServerSocket(8111);
}
catch(IOException e)
{
    e.printStackTrace();
}
```

#### ServerSocket 的常用方法

ServerSocket 的常用方法如下所示。

*   Server accept()：监听并接收到此套接字的连接。
*   void bind(SocketAddress endpoint)：将 ServerSocket 绑定到指定地址（IP 地址和端口号）。
*   void close()：关闭此套接字。
*   InetAddress getInetAddress()：返回此服务器套接字的本地地址。
*   int getLocalPort()：返回此套接字监听的端口。
*   SocketAddress getLocalSoclcetAddress()：返回此套接字绑定的端口的地址，如果尚未绑定则返回 null。
*   int getReceiveBufferSize()：获取此 ServerSocket 的 SO_RCVBUF 选项的值，该值是从 ServerSocket 接收的套接字的建议缓冲区大小。

调用 accept() 方法会返回一个和客户端 Socket 对象相连接的 Socket 对象，服务器端的 Socket 对象使用 getOutputStream() 方法获得的输出流将指定客户端 Socket 对象使用 getInputStream() 方法获得那个输入流。同样，服务器端的 Socket 对象使用的 getInputStream() 方法获得的输入流将指向客户端 Socket 对象使用的 getOutputStream() 方法获得的那个输出流。也就是说，当服务器向输出流写入信息时，客户端通过相应的输入流就能读取，反之同样如此，整个过程如图 1 所示。

图 1 服务器与客户端连接示意图

#### 例 1

了解上面的基础知识后，下面使用 ServerSocket 类在本机上创建一个使用端口 8888 的服务器端套接字，实例代码如下所示。

```
public static void main(String[] args)
{
    try
    {
        //在 8888 端口创建一个服务器端套接字
        ServerSocket serverSocket=new ServerSocket(8888);
        System.out.println("服务器端 Socket 创建成功");
        while(true)
        {
            System.out.println("等待客户端的连接请求");
            //等待客户端的连接请求
            Socket socket=serverSocket.accept();
            System.out.println("成功建立与客户端的连接");
        }
    }
    catch(IOException e)
    {
        e.printStackTrace();
    }
}
```

如上述代码所示，在成功建立 8888 端口的服务器端套接字之后，如果没有客户端的连接请求，则 accept() 方法为空，所以不会输出“成功建立与客户端的连接”，运行结果如下所示。

```
服务器端 S.ocket 创違成功
等待客户端的连接请求
```

## Socket 类

Socket 类表示通信双方中的客户端，用于呼叫远端机器上的一个端口，主动向服务器端发送数据（当连接建立后也能接收数据）。下面简单介绍一下 Socket 类的构造方法和常用方法。

#### Socket 的构造方法

Socket 的构造方法如下所示。

*   Socket()：无参构造方法。
*   Socket(InetAddress address,int port)：创建一个流套接字并将其连接到指定 IP 地址的指定端口。
*   Soclcet(InetAddress address,int port,InetAddress localAddr,int localPort)：创建一个套接字并将其连接到指定远程地址上的指定远程端口。
*   Socket(String host,int port)：创建一个流套接字并将其连接到指定主机上的指定端口。
*   Socket(String host,int port,InetAddress localAddr,int localPort)：创建一个套接字并将其连接到指定远程地址上的指定远程端口。Socket 会通过调用 bind() 函数来绑定提供的本地地址及端口。

在上述方法的参数中，address 指的是远程地址，port 指的是远程端口，localAddr 指的是要将套接字绑定到的本地地址，localPort 指的是要将套接字绑定到的本地端口。

#### Socket 的常用方法

Socket 的常用方法如下所示。

*   void bind(SocketAddress bindpoint)：将套接字绑定到本地地址。
*   void close()：关闭此套接字。
*   void connect(SocketAddress endpoint)：将此套接字连接到服务器。
*   InetAddress getInetAddress()：返回套接字的连接地址。
*   InetAddress getLocalAddress()：获取套接字绑定的本地地址。
*   InputStream getInputStream()：返回此套接字的输入流。
*   OutputStream getOutputStream()：返回此套接字的输出流。
*   SocketAddress getLocalSocketAddress()：返回此套接字绑定的端点地址，如果尚未绑定则返回 null。
*   SocketAddress getRemoteSocketAddress()：返回此套接字的连接的端点地址，如果尚未连接则返回 null。
*   int getLoacalPort()：返回此套接字绑定的本地端口。
*   intgetPort()：返回此套接字连接的远程端口。

#### 例 2

编写 TCP 程序，包括一个客户端和一个服务器端。要求服务器端等待接收客户端发送的内容，然后将接收到的内容输出到控制台并做出反馈。

(1) 创建一个类作为客户端，首先在 main() 方法中定义一个 Socket 对象、一个 OutputStream 对象和一个 InputStream 对象并完成初始化。接着定义服务器端的 IP 地址和端口号，代码如下所示。

```
public static void main(String[] args)
{
    Socket socket=null;
    OutputStream out=null;
    InputStream in=null;
    String serverIP="127.0.0.1";    //服务器端 IP 地址
    int port=5000;    //服务器端端口号
}
```

(2) 建立与服务器端的连接并将数据发送到服务器端，代码如下所示。

```
socket=new Socket(serverIP,port);    //建立连接
out=socket.getOutputStream();    //发送数据
out.write("我是客户端数据 ".getBytes());
```

(3) 从输入流中读出服务器的反馈信息并输出到控制台，代码如下所示。

```
byte[] b=new byte[1024];
in=socket.getInputStream();
int len=in.read(b);
System.out.println(" 服务器端的反馈为："+new String(b,0,len));
```

(4) 关闭输入/输出流以及 Socket 对象，代码如下所示。

```
in.close();
out.close();
socket.close();
```

(5) 创建一个类作为服务器端，编写 main() 方法，创建 ServerSocket、Socket、InputStream、OutputStream 以及端口号并初始化，代码如下所示。

```
ServerSocket ServerSocket=null;
Socket socket=null;
InputStream in=null;
OutputStream out=null;
int port=5000;
```

(6) 开启服务器并接收客户端发送的数据，代码如下所示。

```
ServerSocket=new ServerSocket(port);    //创建服务器套接字
System.out.println("服务器开启，等待连接。。。");
socket=ServerSocket.accept();    //获得连接
//接收客户端发送的内容
in=socket.getInputStream();
byte[] b=new byte[1024];
int len=in.read(b);
System.out.println("客户端发送的内容为："+new String(b,0,len));
```

(7) 使用输出流对象将信息反馈给客户端，代码如下所示。

```
out=socket.getOutputStream();
out.write("我是服务器端".getBytes());
```

(8) 关闭输入/输出流、Socket 对象以及 ServerSocket 对象，代码如下所示。

```
in.close();
out.close();
ServerSocket.close();
socket.close();
```

(9) 运行服务器端程序代码，运行结果如下所示。

```
服务器开启，等待连接。。。
```

(10) 为了使程序的结果更加清晰，在步骤 (2) 的代码最后加入一句代码“Thread.sleep(1000);”。接着运行客户端程序代码，刚开始会出现如下所示的运行结果。

```
服务器开启，等待连接。。。
客户端发送的内容为：我是客户端数据
```

紧接着又会出现如下所示的运行结果。

```
客户端的反馈为：我是服务器端
```

## 客户端与服务器端的简单通信

在了解 TCP 通信中 ServerSocket 类和 Socket 类的简单应用之后，本节将编写一个案例实现客户端向服务器发送信息，服务器读取客户端发送的信息，并将读取的数据写入到数据流中。

首先来看一下客户端的代码，如下所示：

```
public class SocketDemo
{
    public static void main(String[] args)
    {
        Socket socket=null;
        PrintWriter out=null;
        BufferedReader in=null;
        String serverIP="127.0.0.1";    //服务器端 ip 地址
        int port=5000;    //服务器端端口号
        try
        {
            socket=new Socket(serverIP,port);
            in=new BufferedReader(new InputStreamReader(socket.getInputStream()));
            out=new PrintWriter(socket.getOutputStream(),true);
            while(true)
            {
                int number=(int)(Math.random()*10)+1;
                System.out.println("客户端正在发送的内容为："+number);
                out.println(number);
                Thread.sleep(2000);
            }
        }
        catch(IOException | InterruptedException e)
        {
            // TODO 自动生成的 catch 块
            e.printStackTrace();
        }
    }
}
```

如上述代码所示，客户端代码主要是使用 Socket 连接 IP 为 127.0.0.1（本机）的 5000 端口。在建立连接之后将随机生成的数字使用 PrintWriter 类输出到套接字。休眠 2 秒后，再次发送随机数，如此循环。

再来看一个服务器端的代码，如下所示：

```
package ch16;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;
public class SocketDemoServer1
{
    public static void main(String[] args)
    {
        ServerSocket serverSocket=null;
        Socket clientSocket=null;
        BufferedReader in=null;
        int port=5000;
        String str=null;
        try
        {
            serverSocket=new ServerSocket(port);    //创建服务器套接字
            System.out.println("服务器开启，等待连接。。。");
            clientSocket=serverSocket.accept();// 获得链接
            //接收客户端发送的内容
            in=new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
            while(true)
            {
                str=in.readLine();
                System.out.println("客户端发送的内容为："+str);
                Thread.sleep(2000);
            }
        }
        catch(IOException | InterruptedException e)
        {
            // TODO 自动生成的 catch 块
            e.printStackTrace();
        }
    }
}
```

如上述代码所示，服务器端与客户端代码类似，首先使用 ServerSocket 在 IP 为 127.0.0.1（本机）的 5000 端口建立套接字监听。在 accept() 方法接收到客户端的 Socket 实例之后调用 BufferedReader 类的 readLine() 方法，从套接字中读取一行作为数据，再将它输出到控制后休眠 2 秒。

要运行本案例，必须先执行服务器端程序，然后执行客户端程序。客户端每隔 2 秒向服务器发送一个数字，如下所示。

```
客户端正在发送的内容为：10
客户端正在发送的内容为：5
客户端正在发送的内容为：10
客户端正在发送的内容为：4
客户端正在发送的内容为：3
```

服务器端会将客户端发送的数据输出到控制台，如下所示。

```
服务器幵启，等待连接。。。
客户端发送的内容为：7
客户端发送的内容为：2
客户端发送的内容为：10
客户端发送的内容为：5
客户端发送的内容为：10
```

......

## 传输对象数据

经过前面的学习，掌握了如何在服务器开始一个端口监听套接字，以及如何在客户端连接服务器，发送简单的数字。本次案例将实现如何在客户端发送一个对象到服务器端，服务器如何解析对象中的数据。

#### 例 3

第一步是创建用于保存数据的类。这里使用的 User 类是一个普通的类，包含 name 和 password 两个成员。由于需要序列化这个对象以便在网络上传输，所以需要实现 java. io.Serializable 接 P。

User 类的代码如下：

```
package ch16;
public class User implements java.io.Serializable
{
    private String name;
    private String password;
    public User(String name,String password)
    {
        this.name=name;
        this.password=password;
    }
    public String getName()
    {
        return name;
    }
    public void setName(String name)
    {
        this.name=name;
    }
    public String getPassword()
    {
        return password;
    }
    public void setPassword(String password)
    {
        this.password=password;
    }
}
```

接下来编写服务器端的代码。服务器的作用是接收客户端发送过来的数据，将数据转换成 User 对象并输出成员信息，然后对 User 对象进行修改再输出给客户端。

服务器端 MyServer 类的实现代码如下：

```
package ch16;
import java.io.BufferedInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.ServerSocket;
import java.net.Socket;
public class MyServer
{
    public static void main(String[] args) throws IOException
    {
        // 监听 10000 端口
        ServerSocket server=new ServerSocket(10000);
        while(true)
        {
            //接收客户端的连接
            Socket socket=server.accept();
            //调用客户端的数据处理方法
            invoke(socket);
        }
    }
    private static void invoke(final Socket socket) throws IOException
    {
        //开启一个新线程
        new Thread(new Runnable()
        {
            public void run()
            {
                //创建输入流对象
                ObjectInputStream is=null;
                //创建输出流对象
                ObjectOutputStream os=null;
                try
                {
                    is=new ObjectInputStream(socket.getInputStream());
                    os=new ObjectOutputStream(socket.getOutputStream());
                    //读取一个对象
                    Object obj = is.readObject();
                    //将对象转换为 User 类型
                    User user=(User) obj;
                    //在服务器端输出 name 成员和 password 成员信息
                    System.out.println("user: "+user.getName()+"/"+user.getPassword());
                    //修改当前对象的 name 成员数据
                    user.setName(user.getName()+"_new");
                    //修改当前对象的 password 对象数据
                    user.setPassword(user.getPassword()+"_new");
                    //将修改后的对象输出给客户端
                    os.writeObject(user);
                    os.flush();
                }
                catch(IOException|ClassNotFoundException ex)
                {
                    ex.printStackTrace();
                }
                finally
                {
                    try
                    {
                        //关闭输入流
                        is.close();
                        //关闭输出流
                        os.close();
                        //关闭客户端
                        socket.close();
                    }
                    catch(Exception ex){}
                }
            }
        }).start();
    }
}
```

如上述代码所示，在服务器端分别使用 ObjectInputStream 和 ObjectOutputStream 来接收和发送 socket 中的 InputStream 和 OutputStream，然后转换成 User 对象。

客户端需要连接服务器，接收服务器输出的数据并解析，同时需要创建 User 对象并发给服务器。客户端 MyClient 类的实现代码如下：

```
package ch16;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.Socket;
public class MyClient
{
    public static void main(String[] args) throws Exception
    {
        //循环 100 次
        for(int i=0;i<100;i++)
        {
            //创建客户端 Socket
            Socket socket=null;
            //创建输入流
            ObjectOutputStream os=null;
            //创建输出流
            ObjectInputStream is=null
            try
            {
                //连接服务器
                socket=new Socket("localhost",10000);
                //接收输出流中的数据
                os=new ObjectOutputStream(socket.getOutputStream());
                //创建一个 User 对象
                User user=new User("user_"+i,"password_"+i);
                //将 User 对象写入输出流
                os.writeObject(user);
                os.flush();
                //接收输入流中的数据
                is=new ObjectInputStream(socket.getInputStream());
                //读取输入流中的数据
                Object obj=is.readObject();
                //如果数据不空则转换成 User 对象，然后输出成员信息
                if(obj!=null)
                {
                    user=(User) obj;
                    System.out.println("user: "+user.getName()+"/"+user.getPassword());
                }
            }
            catch(IOException ex)
            {
                ex.printStackTrace();
            }
            finally
            {
                try
                {
                    //关闭输入流
                    is.close();
                    //关闭输出流
                    os.close();
                    //关闭客户端
                    socket.close();
                }
                catch(Exception ex) {}
            }
        }
    }
}
```

仔细观察上述代码可以发现，客户端与服务器端的代码类似，同样使用 ObjectOutputStream 和 ObjectInputStream 来处理数据。

先运行服务器端程序 MyServer，再运行客户端程序 MyClient。此时将在客户端看到下所示的输出。

```
user:user_86_nevj/password_86_new
user:user_87_new/password_87_new
user:user_88_new/password_88_new
user:user_89_new/password_89_new
user:user_90_new/password_90_new
user:user_91_new/password_91_new
user:user_92_new/password_92_new
user:user_93_new/password_93_new
user:user_94_new/password_94_new
user:user_95_new/password_95_new
user:user_96_new/password_96_new
user:user_97_new/password_97_new
user:user_98_new/password_98_new
user:user_99_new/password_99_new
```

服务器端的输出如下所示。

```
user:user_86/password_86
user:user_87/password_87
user:user_88/password_88
user:user_89/password_89
user:user_90/password_90
user:user_91/password_91
user:user_92/password_92
user:user_93/password_93
user:user_94/password_94
user:user_95/password_95
user:user_96/password_96
user:user_97/password_97
user:user_98/password_98
user:user_99/password_99
```