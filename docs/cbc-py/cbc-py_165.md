# Python os 模块详解

除前面章节介绍的各种函数之外，os 模块还提供了大量操作文件和目录的函数，本节将介绍 os 模块下常用的函数。

如果读者需要查阅有关这些函数的说明，则可访问 https://docs.python.org/3/library/os.html 页面。

## os 模块与目录相关的函数

与目录相关的函数如下：

*   os.getcwd()：获取当前目录。
*   os.chdir(path)：改变当前目录。
*   os.fchdir(fd)：通过文件描述利改变当前目录。该函数与上一个函数的功能基本相似，只是该函数以文件描述符作为参数来代表目录。

    下面程序测试了与目录相关的函数的用法：

    ```
    import os

    # 获取当前目录
    print(os.getcwd())  # G:\publish\codes\12.7
    # 改变当前目录
    os.chdir('../12.6')
    # 再次获取当前目录
    print(os.getcwd())  # G:\publish\codes\12.6
    ```

    上面程序示范了使用 getcwd() 来获取当前目录，也示范了使用 chdir() 来改变当前目录。
*   os.chroot(path)：改变当前进程的根目录。
*   os.listdir(path)：返回 path 对应目录下的所有文件和子目录。
*   os.mkdir(path[, mode])：创建 path 对应的目录，其中 mode 用于指定该目录的权限。该 mode 参数代表一个 UNIX 风格的权限，比如 0o777 代表所有者可读/可写/可执行、组用户可读/可写/可执行、其他用户可读/可写/可执行。
*   os.makedirs(path[, mode])：其作用类似于 mkdir()，但该函数的功能更加强大，它可以边归创建目录。比如要创建 abc/xyz/wawa 目录，如果在当前目录下没有 abc 目录，那么使用 mkdir() 函数就会报错，而使用 makedirs() 函数则会先创建 abc，然后在其中创建 xyz 子目录，最后在 xyz 子目录下创建 wawa 子目录。

    如下程序示范了如何创建目录：

    ```
    import os
    path = 'my_dir'
    # 直接在当前目录下创建目录
    os.mkdir(path, 0o755)
    path = "abc/xyz/wawa"
    # 递归创建目录
    os.makedirs(path, 0o755)
    ```

    正如从上面代码所看到的，直接在当前目录下创建 mydir 子目录，因此可以使用 mkdir() 函数创建；需要程序递归创建 abc/xyz/wawa 目录，因此使用 makedirs() 函数。
*   os.rmdir(path)：删除 path 对应的空目录。如果目录非空，则抛出一个 OSError 异常。程序可以先用 os.remove() 函数删除文件。
*   os.removedirs(path)：边归删除目录。其功能类似于 rmdir()，但该函数可以递归删除 abc/xyz/wawa 目录，它会从 wawa 子目录开始删除，然后删除 xyz 子目录，最后删除 abc 目录。

    如下程序示范了如何删除目录：

    ```
    import os

    path = 'my_dir'
    # 直接删除当前目录下的子目录
    os.rmdir(path)
    path = "abc/xyz/wawa"
    # 递归删除子目录
    os.removedirs(path)
    ```

    上面程序中第 5 行代码使用 rmdir() 函数删除当前目录下的 my_dir 子目录，该函数不会执行递归删除；第 8 行代码使用 removedirs() 函数删除 abc/xyz/wawa 目录，该函数会执行递归删除，它会先删除 wawa 子目录，然后删除 xyz 子目录，最后才删除 abc 目录。
*   os.rename(src, dst)：重命名文件或目录，将 src 重名为 dst。
*   os.renames(old, new)：对文件或目录进行递归重命名。其功能类似于 rename()，但该函数可以递归重命名 abc/xyz/wawa 目录，它会从 wawa 子目录开始重命名，然后重命名 xyz 子目录，最后重命名 abc 目录。

    如下程序示范了如何重命名目录：

    ```
    import os

    path = 'my_dir'
    # 直接重命名当前目录下的子目录
    os.rename(path, 'your_dir')
    path = "abc/xyz/wawa"
    # 递归重命名子目录
    os.renames(path, 'foo/bar/haha')
    ```

    上面程序中第 5 行代码直接重命名当前目录下的 my_dir 子目录，程序会将该子目录重命名为 your_dir；第 8 行代码则执行递归重命名，程序会将 wawa 重命名为 haba，将 xyz 重命名为 bar，将 abc 重命名为 foo。

## os 模块与权限相关的函数

与权限相关的函数如下：

*   os.access(path, mode)：检查 path 对应的文件或目录是否具有指定权限。该函数的第二个参数可能是以下四个状态值的一个或多个值：
    *   os.F_OK：判断是否存在。
    *   os.R_OK：判断是否可读。
    *   os.W_OK：判断是否可写。
    *   os.X_OK：判断是否可执行。
    例如如下程序：

    ```
    import os

    # 判断当前目录的权限
    ret = os.access('.', os.F_OK|os.R_OK|os.W_OK|os.X_OK)
    print("os.F_OK|os.R_OK|os.W_OK|os.X_OK - 返回值:", ret)
    # 判断 os.access_test.py 文件的权限
    ret = os.access('os.access_test.py', os.F_OK|os.R_OK|os.W_OK)
    print("os.F_OK|os.R_OK|os.W_OK - 返回值:", ret)
    ```

    上面程序判断当前目录的权限和当前文件的权限，这里特意将此文件设为只读的。运行该程序，可以看到如下输出结果：

    os.F_OK|os.R_OK|os.W_OK|os.X_OK - 返回值：True
    os.F_OK|os.R_OK|os.W_OK - 返回值：False

*   os.chrnod(path, mode)：更改权限。其中 mode 参数代表要改变的权限，该参数支持的值可以是以下一个或多个值的组合：
    *   stat.S_IXOTH：其他用户有执行权限。
    *   stat.S_IWOTH：其他用户有写权限。
    *   stat.S_TROTH：其他用户有读权限。
    *   stat.S_IRWXO：其他用户有全部权限。
    *   stat.S_IXGRP：组用户有执行权限。
    *   stat.S_IWGRP：组用户有写权限。
    *   stat.S_IRGRP：组用户有读权限。
    *   stat.S_IRWXG：组用户有全部权限。
    *   stat.S_IXUSR：所有者有执行权限。
    *   stat.S_IWUSR：所有者有写权限。
    *   stat.S_IRUSR：所有者有读权限。
    *   stat.S_IRWXU：所有者有全部权限。
    *   stat.S_IREAD：Windows 将该文件设为只读的。
    *   stat.S_IWRITE：Windows 将该文件设为可写的。

    前面的那些权限都是 UNIX 文件系统下有效的概念，UNIX 文件系统下的文件有一个所有者，跟所有者处于同一组的其他用户被称为组用户。因此在 UNIX 文件系统下允许为不同用户分配不同的权限。

    例如如下程序：

    ```
    import os, stat

    # 将 os.chmod_test.py 文件改为只读
    os.chmod('os.chmod_test.py', stat.S_IREAD)
    # 判断是否可写
    ret = os.access('os.chmod_test.py', os.W_OK)
    print("os.W_OK - 返回值:", ret)
    ```

    运行上面程序后，os.chmod_test.py 变成只读文件。
*   os.chown(path, uid, gid)：更改文件的所有者。其中 uid 代表用户 id，gid 代表组 id。该命令主要在 UNIX 文件系统下有效。
*   os.fchmod(fd, mode)：改变一个文件的访问权限，该文件由文件描述符 fd 指定。该函数的功能与 os.chmod() 函数的功能相似，只是该函数使用 fd 代表文件。
*   os.fchown(fd, uid, gid)：改变文件的所有者，该文件由文件描述符 fd 指定。该函数的功能与 os.chown() 函数的功能相似，只是该函数使用 fd 代表文件。

## os 模块与文件访问相关的函数

与文件访问相关的函数如下：

*   os.open(file, flags[, mode])：打开一个文件，并且设置打开选项，mode 参数是可选的。该函数返回文件描述符。其中 flags 代表打开文件的旗标，它支持如下一个或多个选项：
    *   os.O_RDONLY：以只读的方式打开。
    *   os.O_WRONLY：以只写的方式打开。
    *   os.O_RDWR：以读写的方式打开。
    *   os.O_NONBLOCK：打开时不阻塞。
    *   os.O_APPEND：以追加的方式打开。
    *   os.O_CREAT：创建并打开一个新文件。
    *   os.O_TRUNC：打开一个文件并截断它的长度为 0（必须有写权限）。
    *   os.O_EXCL：在创建文件时，如果指定的文件存在，则返回错误。
    *   os.O_SHLOCK：自动获取共享锁。
    *   os.O_EXLOCK：自动获取独立锁。
    *   os.O_DIRECT：消除或减少缓存效果。
    *   os.O_FSYNC：同步写入。
    *   os.O_NOFOLLOW：不追踪软链接。
*   os.read(fd, n)：从文件描述符 fd 中读取最多 n 个字节，返回读到的字符串。如果文件描述符副对应的文件己到达结尾，则返回一个空字节串。
*   os.write(fd, str)：将字节串写入文件描述符 fd 中，返回实际写入的字节串长度。
*   os.close(fd)：关闭文件描述符 fd。
*   os.lseek(fd, pos, how)：该函数同样用于移动文件指针。其中 how 参数指定从哪里开始移动，如果将 how 设为 0 或 SEEK_SET，则表明从文件开头开始移动；如果将 how 设为 1 或 SEEK_CUR，则表明从文件指针当前位置开始移动；如果将 how 设为 2 或 SEEK_END，则表明从文件结束处开始移动。上面几个函数同样可用于执行文件的读写，程序通常会先通过 os.open() 打开文件，然后调用 os.read()、os.write() 来读写文件，当操作完成后通过 os.close() 关闭文件。

    如下程序示范了使用上面的函数来读写文件：

    ```
    import os

    # 以读写、创建方式打开文件
    f = os.open('abc.txt', os.O_RDWR|os.O_CREAT)
    # 写入文件内容
    len1 = os.write(f, '水晶潭底银鱼跃，\n'.encode('utf-8'))
    len2 = os.write(f, '清徐风中碧竿横。\n'.encode('utf-8'))
    # 将文件指针移动到开始处
    os.lseek(f, 0, os.SEEK_SET)
    # 读取文件内容
    data = os.read(f, len1 + len2)
    # 打印读取到字节串
    print(data)
    # 将字节串恢复成字符串
    print(data.decode('utf-8'))
    os.close(f)
    ```

    上面程序中，第 6、7 行代码用于向所打开的文件中写入数据；第 11 行代码用于读取文件内容。
*   os.fdopen(fd[, mode[, bufsize]])：通过文件描述符 fd 打开文件，并返回对应的文件对象。
*   os.closerange(fd_low, fd_high)：关闭从 fd_low（包含）到 fd_high（不包含）范围的所有文件描述符。
*   os.dup(fd)：复制文件描述符。
*   os.dup2(fd,fd2)：将一个文件描述符 fd 复制到另一个文件描述符 fd2 中。
*   os.ftruncate(fd, length)：将 fd 对应的文件截断到 length 长度，因此此处传入的 length 参数不应该超过文件大小。
*   os.remove(path)：删除 path 对应的文件。如果 path 是一个文件夹，则抛出 OSError 错误。如果要删除目录，则使用 os.rmdir()。
*   os.link(src, dst)：创建从 src 到 dst 的硬链接。硬链接是 UNIX 系统的概念，如果在 Windows 系统中就是复制目标文件。
*   os.symlink(src, dst)：创建从 src 到 dst 的符号链接，对应于 Windows 的快捷方式。

由于 Windows 权限的缘故，因此必须以管理员身份执行 os.symlink() 函数来创建快捷方式。

下面程序示范了在 Windows 系统中使用 os.symlink(src, dst) 函数来创建快捷方式：

```
import os

# 为 os.link_test.py 文件创建快捷方式
os.symlink('os.link_test.py', 'tt')
# 为 os.link_test.py 文件创建硬连接（Windows 上就是复制文件）
os.link('os.link_test.py', 'dst')
```

上面程序使用 symlink() 函数为指定文件创建符号链接，在 Windows 系统中就是创建快捷方式；使用 link() 函数创建硬链接，在 Windows 系统中就是复制文件。

运行上面程序，将会看到程序在当前目录下创建了一个名为“tt”的快捷方式，并将 os.link_test.py 文件复制为 dst 文件。