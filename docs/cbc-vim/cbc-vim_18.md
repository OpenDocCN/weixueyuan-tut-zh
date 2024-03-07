# vi 字符串替换命令

vi 编辑器提供简单的字符串替换命令，在末行模式下可以使用替换命令，其命令格式如下：

[range]s/s1/s2/ [option]

*   [range] 表示检索范围，省略时表示当前行。下面是一些检索范围的例子。
    *   `1,10`表示从第 1 行到 10 行。
    *   `%`表示整个文件，同`1, $`。
    *   `. ,$`从当前行到文件尾。
*   s 为替换命令。
*   s1 要被替换的串，s2 为替换的串。
*   option 表示选项：
    *   `/g`表示在全局文件中进行替换。
    *   `/c`表示在每次替换之前需要用户进行确认。
    *   省略时仅对每行第一个匹配串进行替换。

下面实例演示了字符串替换命令，将第 10~22 行的 printf 标识符替换为 PRINT。

1) 在 vi 编辑器中编辑以下代码：

int main(void)
{
    struct stat buf; /*存储文件状态信息*/
    if(stat("s1", &buf) == -1) {
        perror ("fail to stat");
        exit(1);
    }
    printf("permission : %d\n", buf.st_mode);
    printf("inode number : %d\n", buf.st_ino);
    printf("device number : %d\n", buf.st_dev);
    printf("r-device number : %d\n", buf.st_rdev);
    printf("link : %d\n", buf.st_nlink);
    printf("uid : %d\n", buf.st_uid);
    printf("gid : %d\n", buf.st_gid);
    printf("file size : %d\n", buf.st_size);
    printf("access time : %d\n", buf.st_atime);
    printf("motify time : %d\n", buf.st_mtime);
    printf("change time : %d\n", buf.st_ctime);
    printf("buf size : %d\n", buf.st_blksize);
    printf("block size : %d\n", buf.st_blocks);
    return 0;
}

2) 在末行模式下输入以下替换命令：

10,22s/printf/PRINT/g

屏幕显示如下：

int main(void)
{
    struct stat buf; /*存储文件状态信息*/
    if(stat("s1", &buf) == -1) {
        perror ("fail to stat");
        exit(1);
    }
    PRINTF("permission : %d\n", buf.st_mode);
    PRINTF("inode number : %d\n", buf.st_ino);
    PRINTF("device number : %d\n", buf.st_dev);
    PRINTF("r-device number : %d\n", buf.st_rdev);
    PRINTF("link : %d\n", buf.st_nlink);
    PRINTF("uid : %d\n", buf.st_uid);
    PRINTF("gid : %d\n", buf.st_gid);
    PRINTF("file size : %d\n", buf.st_size);
    PRINTF("access time : %d\n", buf.st_atime);
    PRINTF("motify time : %d\n", buf.st_mtime);
    PRINTF("change time : %d\n", buf.st_ctime);
    PRINTF("buf size : %d\n", buf.st_blksize);
    PRINTF("block size : %d\n", buf.st_blocks);
    return 0;
}

vi 编辑器在执行替换命令指令时，可以进行简单的模式匹配，其匹配模式如下：

\<word

替换以 word 开始的单词的 word 部分。例如：

%s/\<abc/cde

表示将以 abc 开头的单词替换为以 cde 开头。

下例演示了这种替换操作，更改 stat 结构的变量名，将整个文件中的 buf 替换为 stat_buf。

1) 在 vi 编辑器中编辑以下代码：

int main(void)
{
    struct stat buf; /*存储文件状态信息*/
    if(stat("s1", &buf) == -1) {
        perror ("fail to stat");
        exit(1);
    }
    printf("permission : %d\n", buf.st_mode);
    printf("inode number : %d\n", buf.st_ino);
    printf("device number : %d\n", buf.st_dev);
    printf("r-device number : %d\n", buf.st_rdev);
    printf("link : %d\n", buf.st_nlink);
    printf("uid : %d\n", buf.st_uid);
    printf("gid : %d\n", buf.st_gid);
    printf("file size : %d\n", buf.st_size);
    printf("access time : %d\n", buf.st_atime);
    printf("motify time : %d\n", buf.st_mtime);
    printf("change time : %d\n", buf.st_ctime);
    printf("buf size : %d\n", buf.st_blksize);
    printf("block size : %d\n", buf.st_blocks);
    return 0;
}

2) 在末行模式下输入命令：

%s/\<buf/stat_buf/g

屏幕显示如下：

int main(void)
{
    struct stat stat_buf; /*存储文件状态信息*/
    if(stat("s1", &stat_buf) == -1) {
        perror ("fail to stat");
        exit(1);
    }
    printf("permission : %d\n", stat_buf.st_mode);
    printf("inode number : %d\n", stat_buf.st_ino);
    printf("device number : %d\n", stat_buf.st_dev);
    printf("r-device number : %d\n", stat_buf.st_rdev);
    printf("link : %d\n", stat_buf.st_nlink);
    printf("uid : %d\n", stat_buf.st_uid);
    printf("gid : %d\n", stat_buf.st_gid);
    printf("file size : %d\n", stat_buf.st_size);
    printf("access time : %d\n", stat_buf.st_atime);
    printf("motify time : %d\n", stat_buf.st_mtime);
    printf("change time : %d\n", stat_buf.st_ctime);
    printf("stat_buf size : %d\n", stat_buf.st_blksize);
    printf("block size : %d\n", stat_buf.st_blocks);
    return 0;
}