# Mysql 安装注意事项、安装失败的五个原因

**错误 1：wizard 安装最后一页，出现 cannot create Windows service for mysql.error:0 错误**

解决方法：打开命令行 输入 sc delete mysql 命令即
   C:>sc delete mysql
   [SC] DeleteService SUCCESS
重起电脑，或者卸载后重启再安装，这样就好了。我是卸载后重启的。

卸载重新安装要注意一个问题，在控制面板中卸载后，要去 c:\Documents and Settings\All Users\Application Data 目录下把 Mysql 文件夹删掉。里面可能记载了你卸载的配置信息，下次安装时会有影响。

**错误 2：在让输入管理员输入密码时除了 password 和 confirm password 之外还有一行 current password。**

这说明你安装之前没有把我第 1 条中说的目录下把 Mysql 文件夹删除。不知道是否影响后续使用，可能记得密码的话没关系。另附带一句，管理员密码是自己设的，用户名则为默认 root。

安装成功后可测试如下：打开 mysql command line client,输入密码，出现
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.1.33-community MySQL Community Server (GPL)
Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
mysql>
则安装成功！

**错误 3：mysql server configuration 失败。**

在配置向导中查看错误提示有如下语句：
   mysql-server-5.5-win32:60 - Adding firewall rule for MySQL55 on port 3306.
   mysql-server-5.5-win32:66 - Adding firewall rule failed.
表示防火墙安装失败。

查看安装日志能找到以下语句：
   mysql-installer Information: 10 : Attempting to create firewall rule with command: netsh.exe  firewall add portopening protocol=TCP port=3306 profile=ALL name=MySQL55 mode=ENABLE scope=ALL
   mysql-installer Information: 10 : Unexpected response from netsh: 确定。
    mysql-installer Information: 10 : Attempting to create firewall rule with command: netsh.exe  advfirewall firewall add rule name=”Port 3306” protocol=TCP localport=3306 dir=in action=allow
   mysql-installer Information: 10 : Unexpected response from netsh: 下列命令没有找到: advfirewall firewall add rule name=”Port 3306” protocol=TCP localport=3306 dir=in action=allow。

这种情况说明 mysql 的安装对中文系统支持得不好，不能识别 netsh.exe 返回的“确定”信息，继而运行能在其他 windows 平台上运行的 netsh.exe advfirewall 命令（该命令 xp 不支持，所以报找不到该命令的错误。

解决办法有两个：  
①将自己的操作系统的语言设置为英文，然后重新安装 mysql  。具体的可以进入控制面板->区域和语言  中去更改；另外，检查当前用户是否有启动防火墙的权限，在计算机服务中查看 Application Layer Gateway Service 的属性，查看登录选项卡中的用户是否是当前用户。
②检查防火墙端口，如果已经添加成功了，则不作处理，如果没有成功添加，则自己手动在防火墙中添加端口。

**错误 4：安装成功之后，只能通过 mysql 5.5 command line client 连接 localhost 的 mysql server，而不能通过 workbench 或其他客户端工具进行远程连接 mysql server。**

关键字：不能远程连接 mysql server。

可能原因：
①没有开端口，参见前一条解决。
②用户没有授权远程连接。
③mysql server 不允许远程连接。

对于②用户没有授权远程连接，可以给用户授权，例如默认的 root 用户，可以在 mysql 5.5 command line client 中输入如下命令：
   //选择数据库
   mysql>use mysql;
   //给用户授权，yourpassword 字段用你的密码代替
   mysql>grant all priileges on *.* to  identified by 'yourpassword';
   //刷新系统权限表
   mysql>flush privileges;

   //查看是否授权成功
   mysql> show grants for roots;

如果出现刚刚的授权，则表示成功。不成功的话，自己再添加一个新用户试试，添加用户的命令：
   insert into mysql.user(Host,User,Password,ssl_cipher,x509_issuer,x509_subject) values("%","newuser",password("newpassword"),'','','')

对于③mysql server 不允许远程连接的解决办法是，找到自己的 my.ini，在[mysqld]下增加一行语句：
   bind-address=0.0.0.0
表示不绑定 IP，重启你的 Mysql 服务。

**错误 5：配置文件 my.ini 的位置**

MySQL 配置向导将 my.ini 文件放置在 MySQL 服务器的安装目录中。这将有助于将配置文件和具体的服务器实例相 关联。为了保证 MySQL 服务器知道到哪里查找 my.ini 文件，和下面内容类似的参数将会被作为服务安装的一部分 传递给 MySQL 服务器：--defaults-file="C:\Program Files\MySQL\MySQL Server 5.5\my.ini C:\Program Files\MySQL\MySQL Server 5.5 可以被指向 MySQL 服务器的安装路径所代替。

编辑 my.ini 文：可以使用文本编辑器打开该文件同时做出必要的编辑和修改。你也可以以 MySQL Administrator 的应用程序来修 改服务器配置。 MySQL 客户端和应用程序，例如 mysql 命令行客户端和 mysqldump 并不能确定位于服务器安装目录中的 my.ini 文件 的位置。为了配置客户端和应用程序，根据你的 Windows 版本的不同，在 C:\Windows 下或者在 C:\WINNT 目录下生 成新的文件 my.ini 文件

如果你的 MySQL 配置向导发现了一个已经存在的 my.ini 文件，你可以重新配置已经存在的服务器，或者通过删除 my.ini 文件、停止并移除 MySQL 服务的方法移除服务器实例。重新配置已经存在的服务器，选择“重新配置实例”选项并且选择“下一步”按钮。已经存在的 my.ini 文件被 重新命名为 mytimestamp.ini.bak，时间戳是已经存在的 my.ini 创建时的日期和时间。移除已经存在的数据库实 例，选择“移除实例”选项并选择“下一步”按钮。如果选择了“移除实例”选项，进入到确认界面。单击“运行”按钮：MySQL 配置向导停止并开始移除 MySQL 服 务，并删除 my.ini 文件。但服务器安装目录并没有移除。如果选择了“重新配置实例”选项，进入到“配置类型”界面，可以选择安装想要配置的安装类型。