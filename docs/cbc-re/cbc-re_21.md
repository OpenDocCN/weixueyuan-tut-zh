# C 语言正则表达式详解 regcomp() regexec() regfree()详解

标准的 C 和 C++都不支持正则表达式，但有一些函数库可以辅助 C/C++程序员完成这一功能，其中最著名的当数 Philip Hazel 的 Perl-Compatible Regular Expression 库，许多 Linux 发行版本都带有这个函数库。

C 语言处理正则表达式常用的函数有 regcomp()、regexec()、regfree()和 regerror()，一般分为三个步骤，如下所示：

**C 语言中使用正则表达式一般分为三步：**

1.  编译正则表达式 regcomp()
2.  匹配正则表达式 regexec()
3.  释放正则表达式 regfree()

**下边是对三个函数的详细解释**

1、int regcomp (regex_t *compiled, const char *pattern, int cflags)
这个函数把指定的正则表达式 pattern 编译成一种特定的数据格式 compiled，这样可以使匹配更有效。函数 regexec 会使用这个数据在目标文本串中进行模式匹配。执行成功返回０。 　

**参数说明：**
①regex_t 是一个结构体数据类型，用来存放编译后的正则表达式，它的成员 re_nsub 用来存储正则表达式中的子正则表达式的个数，子正则表达式就是用圆括号包起来的部分表达式。
②pattern 是指向我们写好的正则表达式的指针。
③cflags 有如下 4 个值或者是它们或运算(|)后的值：
REG_EXTENDED 以功能更加强大的扩展正则表达式的方式进行匹配。
REG_ICASE 匹配字母时忽略大小写。
REG_NOSUB 不用存储匹配后的结果。
REG_NEWLINE 识别换行符，这样'$'就可以从行尾开始匹配，'^'就可以从行的开头开始匹配。

2\. int regexec (regex_t *compiled, char *string, size_t nmatch, regmatch_t matchptr [], int eflags)
当我们编译好正则表达式后，就可以用 regexec 匹配我们的目标文本串了，如果在编译正则表达式的时候没有指定 cflags 的参数为 REG_NEWLINE，则默认情况下是忽略换行符的，也就是把整个文本串当作一个字符串处理。执行成功返回０。
regmatch_t 是一个结构体数据类型，在 regex.h 中定义：             
typedef struct
{
   regoff_t rm_so;
   regoff_t rm_eo;
} regmatch_t;
成员 rm_so 存放匹配文本串在目标串中的开始位置，rm_eo 存放结束位置。通常我们以数组的形式定义一组这样的结构。因为往往我们的正则表达式中还包含子正则表达式。数组 0 单元存放主正则表达式位置，后边的单元依次存放子正则表达式位置。

**参数说明：**
①compiled 是已经用 regcomp 函数编译好的正则表达式。
②string 是目标文本串。
③nmatch 是 regmatch_t 结构体数组的长度。
④matchptr regmatch_t 类型的结构体数组，存放匹配文本串的位置信息。
⑤eflags 有两个值
REG_NOTBOL 按我的理解是如果指定了这个值，那么'^'就不会从我们的目标串开始匹配。总之我到现在还不是很明白这个参数的意义；
REG_NOTEOL 和上边那个作用差不多，不过这个指定结束 end of line。

3\. void regfree (regex_t *compiled)
当我们使用完编译好的正则表达式后，或者要重新编译其他正则表达式的时候，我们可以用这个函数清空 compiled 指向的 regex_t 结构体的内容，请记住，如果是重新编译的话，一定要先清空 regex_t 结构体。

4\. size_t regerror (int errcode, regex_t *compiled, char *buffer, size_t length)
当执行 regcomp 或者 regexec 产生错误的时候，就可以调用这个函数而返回一个包含错误信息的字符串。

**参数说明：**
①errcode 是由 regcomp 和 regexec 函数返回的错误代号。
②compiled 是已经用 regcomp 函数编译好的正则表达式，这个值可以为 NULL。
③buffer 指向用来存放错误信息的字符串的内存空间。
④length 指明 buffer 的长度，如果这个错误信息的长度大于这个值，则 regerror 函数会自动截断超出的字符串，但他仍然会返回完整的字符串的长度。所以我们可以用如下的方法先得到错误字符串的长度。

size_t length = regerror (errcode, compiled, NULL, 0);

**下边是一个匹配 Email 例子，按照上面的三步就可以。**![](img/2f37958ff444caa663ab9266e38e02b7.jpg)

**下面的程序负责从命令行获取正则表达式，然后将其运用于从标准输入得到的每行数据，并打印出匹配结果。**
#include <stdio.h>
#include <sys/types.h>
#include <regex.h>

/* 取子串的函数 */
static char* substr(const char*str,
unsigned start, unsigned end)
{
  unsigned n = end - start;
  static char stbuf[256];
  strncpy(stbuf, str + start, n);
  stbuf[n] = 0;
  return stbuf;
}

/* 主程序 */
int main(int argc, char** argv)
{
  char * pattern;
  int x, z, lno = 0, cflags = 0;
  char ebuf[128], lbuf[256];
  regex_t reg;
  regmatch_t pm[10];
  const size_t nmatch = 10;
  /* 编译正则表达式*/
  pattern = argv[1];
  z = regcomp(?, pattern, cflags);
  if (z != 0){
    regerror(z, ?, ebuf, sizeof(ebuf));
    fprintf(stderr, "%s: pattern '%s' \n",ebuf, pattern);
    return 1;
  }
  /* 逐行处理输入的数据 */
  while(fgets(lbuf, sizeof(lbuf), stdin))
  {
    ++lno;
    if ((z = strlen(lbuf)) > 0 && lbuf[z-1] == '\n')
    lbuf[z - 1] = 0;
    /* 对每一行应用正则表达式进行匹配 */
    z = regexec(?, lbuf, nmatch, pm, 0);
    if (z == REG_NOMATCH) continue;
    else if (z != 0) {
      regerror(z, ?, ebuf, sizeof(ebuf));
      fprintf(stderr, "%s: regcom('%s')\n", ebuf, lbuf);
      return 2;
    }
    /* 输出处理结果 */
    for (x = 0; x < nmatch && pm[x].rm_so != -1; ++ x)
    {
      if (!x) printf("%04d: %s\n", lno, lbuf);
      printf(" $%d='%s'\n", x, substr(lbuf, pm[x].rm_so, pm[x].rm_eo));
    }
  }
  /* 释放正则表达式 */
  regfree(?);
  return 0;
}

执行下面的命令可以编译并执行该程序：
# gcc regexp.c -o regexp
# ./regexp 'regex[a-z]*' < regexp.c
0003: #include <regex.h>
$0='regex'
0027: regex_t reg;
$0='regex'
0054: z = regexec(?, lbuf, nmatch, pm, 0);
$0='regexec'

小结：对那些需要进行复杂数据处理的程序来说，正则表达式无疑是一个非常有用的工具。本文重点在于阐述如何在 C 语言中利用正则表达式来简化字符串处理，以便在数据处理方面能够获得与 Perl 语言类似的灵活性。