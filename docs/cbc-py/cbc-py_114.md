# Python re 正则表达式模块及其用法

正则表达式（Regular Expression）用于描述一种字符串匹配的模式，它可用于检查一个字符串是否含有某个子串，也可用于从字符串中提取匹配的子串，或者对字符串中匹配的子串执行替换操作。

很多读者都会觉得正则表达式是非常神奇、高级的知识，实际上正则表达式确实是一种非常实用的工具。正则表达式的入门并不难，任意字符串都可以被当成正则表达式来使用，例如“abc”，它也是一个正则表达式，只是它只能匹配“abc”字符串。

当然，如果正则表达式仅能匹配“abc”这样的字符串，那么正则表达式也就不值得学习了。事实上，正则表达式包含的知识点比较多，它的模式匹配能力也非常强，初学者可以由浅入深地学习。

对于 Python 开发者来说，掌握正则表达式确实是一个很重要的技能。在掌握了正则表达式之后，Python 开发者也可使用正则表达式来开发数据抓取、网络爬虫等程序。

实际上，掌握 Python 的正则表达式并不难，无非就是几个简单的函数。在 Python 的交互式解释器中先导入 re 模块，然后输入 re.__all__ 命令，即可看到该模块所包含的全部属性和函数：

>>> re.__all__
'match', 'fullmatch', 'search', 'sub', 'subn', 'split', 'findall', 'finditer', 'compile', 'purge', 'template', 'escape', 'error', 'A', 'I', 'L', 'M', 'S', 'X', 'U', '[ASCII', 'IGNORECASE', 'LOCALE', 'MULTILINE', 'DOTALL', 'VERBOSE', 'UNICODE']

从上面的输出结果可以看出，re 模块包含了为数不多的几个函数和属性（用于控制正则表达式匹配的几个选项）。下面先介绍这些函数的作用：

*   re.compile(pattern, flags=0)：该函数用于将正则表达式字符串编译成 _sre.SRE_Pattern 对象，该对象代表了正则表达式编译之后在内存中的对象，它可以缓存并复用正则表达式字符串。如果程序需要多次使用同一个正则表达式字符串，则可考虑先编译它。
    该函数的 pattern 参数就是它所编译的正则表达式字符串，flags 则代表了正则表达式的匹配旗标。编译得到的 _sre.SRE_Pattern 对象包含了 re 模块中绝大部分函数对应的方法。比如下面两行代码表示先编译正则表达式，然后调用正则表达式的 search() 方法执行匹配：

    ```
    #先编译正则表达式
    p = re.compile('abc')
    #调用 _sre.SRE_Pattern 对象的 search()方法
    p.search("www.abc.com")
    ```

    上面两行代码和下面代码的效果基本相同：

    ```
    #直接用正则表达式匹配目标字符串
    re.search('abc','www.abc.com')
    ```

    对于上面两种方式，由于第一种方式预编译了正则表达式，因此程序可复用 p 对象（该对象缓存了正则表达式字符串），所以具有更好的性能。
*   re.match(pattern, string, flags=0)：尝试从字符串的开始位置来匹配正则表达式，如果从开始位置匹配不成功，match() 函数就返回 None 。其中 pattern 参数代表正则表达式；string 代表被匹配的字符串；flags 则代表正则表达式的匹配旗标。该函数返回 _sre.SRE_Match 对象，该对象包含的 span(n) 方法用于获取第 n+1 个组的匹配位置，group(n) 方法用于获取第 n+1 个组所匹配的子串。
*   re.search(pattern, string, flags=0)：扫描整个字符串，并返回字符串中第一处匹配 pattern 的匹配对象。其中 pattern 参数代表正则表达式；string 代表被匹配的字符串；flags 则代表正则表达式的匹配旗标。该函数也返回 _sre.SRE_Match 对象。
    根据上面介绍不难发现，match() 与 search() 的区别在于，match() 必须从字符串开始处就匹配，但 search() 可以搜索整个字符串。例如如下程序：

    ```
    import re
    m1 = re.match('www', 'www.fkit.org')# 开始位置可以匹配
    print(m1.span())  # span 返回匹配的位置
    print(m1.group()) # group 返回匹配的组
    print(re.match('fkit', 'www.fkit.com')) # 开始位置匹配不到，返回 None
    m2 = re.search('www', 'www.fkit.org') # 开始位置可以匹配
    print(m2.span())
    print(m2.group())
    m3 = re.search('fkit', 'www.fkit.com') # 中间位置可以匹配，返回 Match 对象
    print(m3.span())
    print(m3.group())
    ```

    运行上面程序，可以看到如下输出结果：

    (0, 3)
    www
    None
    (0, 3)
    www
    (4, 8)
    fkit

    从上面的输出结果可以看出，match() 函数要求必须从字符串开始处匹配，而 search() 函数则可扫描整个字符串，从中间任意位置开始匹配。
*   re.findall(pattern, string, flags=0)：扫描整个字符串，并返回字符串中所有匹配 pattern 的子串组成的列表。其中 pattern 参数代表正则表达式；string 代表被匹配的宇符串；flags 则代表正则表达式的匹配旗标。
*   re.finditer(pattern, string, flags=0)：扫描整个字符串，并返回字符串中所有匹配 pattern 的子串组成的迭代器，迭代器的元素是 _sre.SRE_Match 对象。其中 pattern 参数代表正则表达式；string 代表被匹配的字符串；flags 则代表正则表达式的匹配旗标。

    从上面介绍不难看出，findall() 与 finditer() 函数的功能基本相似，区别在于它们的返回值不同，findall() 函数返回所有匹配 patten 的子串组成的列表；而 finditer() 函数则返回所有匹配 pattern 的子串组成的选代器。

    如果对比 findall()、finditer() 和 search() 函数，它们的区别也很明显，search() 只返回字符串中第一处匹配 pattern 的子串；而 findall() 和 finditer() 则返回字符串中所有匹配 pattern 的子串。

    ```
    import re
    # 返回所有匹配 pattern 的子串组成的列表, 忽略大小写
    print(re.findall('fkit', 'FkIt is very good , Fkit.org is my favorite' , re.I))
    # 返回所有匹配 pattern 的子串组成的迭代器, 忽略大小写
    it = re.finditer('fkit', 'FkIt is very good , Fkit.org is my favorite' , re.I)
    for e in it:
        print(str(e.start()) + "-->" + e.group())
    ```

*   re.fullmatch(pattem, string, flags=0)：该函数要求整个字符串能匹配 pattern，如果匹配则返回包含匹配信息的 _sre.SRE_Match 对象；否则返回 None。
*   re.sub(pattern, repl, string, count=0, flags=0)：该函数用于将 string 字符串中所有匹配 pattern 的内容替换成 repl；repl 既可是被替换的字符串，也可是一个函数。count 参数控制最多替换多少次，如果指定 count 为 0 ，则表示全部首换。如下程序示范了 sub() 函数的简单用法：

    ```
    import re
    my_date = '2008-08-18'
    # 将 my_date 字符串里中画线替换成斜线
    print(re.sub(r'-', '/' , my_date))
    # 将 my_date 字符串里中画线替换成斜线，只替换一次
    print(re.sub(r'-', '/' , my_date, 1))
    ```

    运行上面程序，可以看到如下输出结果：

    2008/08/18
    2008/08-18

    上面程序所使用的 r'-' 是原始字符串，其中 r 代表原始字符串，通过使用原始字符串，可以避免对字符串中的特殊字符进行转义。

    在某些情况下，所执行的替换要基于被替换内容进行改变。比如下面程序需要将字符串中的每个英文单词都变成一套教程的名字：

    ```
    # 在匹配的字符串前后添加内容
    def fun(matched):
        # matched 就是匹配对象，通过该对象的 group()方法可获取被匹配的字符串
        value = "《基础" + (matched.group('lang')) + "教程》"
        return value
    s = 'Python 很好，Kotlin 也很好'
    # 将 s 里面的英文单词（用 re.A 旗标控制）进行替换
    # 使用 fun 函数指定替换的内容
    print(re.sub(r'(?P<lang>\w+)', fun, s, flags=re.A))
    ```

    上面程序使用 sub() 函数执行替换时，指定使用 fun() 函数作为替换内容，而 fun() 函数则负责在 pattern 匹配的字符串之前添加“《基础”，在 pattern 匹配的字符串之后添加“教程》”。运行上面程序，可以看到如下输出结果：

    《基础 Python 教程》很好，《基础 Kotlin 教程》也很好

    由于此时还未深入介绍正则表达式的语法，因此前面所使用的正则表达式都很简单，但此处使用了一个稍微复杂的正则表达式： r'(? P<lang>\w+)'。

    r'(?P<lang>\w+)' 正则表达式用圆括号表达式创建了一个组，并使用“?P”选项为该组起名为 lang（所起的组名要放在尖括号内）。剩下的“\w+”才是正则表达式的内容，其中“\w”代表任意字符；“+”用于限定前面的“\w”可出现一次到多次，因此“\w+”代表一个或多个任意字符。又由于程序执行 sub() 函数时指定了 re.A 选项，这样“\w”就只能代表 ASCII 字符，不能代表汉字。

    当使用 sub() 函数执行替换时，正则表达式“\w+”所匹配的内容可以通过组名“lang”来获取，这样 fun() 函数就调用了 matched.group('lang') 来获取“\w+”所匹配的内容。
*   re.split(pattem, string, maxsplit=0, flags=0)：使用 pattern 对 string 进行分割，该函数返回分割得到的多个子串组成的列表。其中 maxsplit 参数控制最多分割几次。如下程序示范了 split() 函数的用法：

    ```
    import re
    # 使用逗号对字符串进行分割
    print(re.split(', ', 'fkit, fkjava, crazyit'))
    # 输出：['fkit', 'fkjava', 'crazyit']
    # 指定只分割 1 次，被切分成 2 个子串
    print(re.split(', ', 'fkit, fkjava, crazyit', 1))
    # 输出：['fkit', 'fkjava, crazyit']
    # 使用 a 进行分割
    print(re.split('a', 'fkit, fkjava, crazyit'))
    # 输出：['fkit, fkj', 'va, crazyit']
    # 使用 x 进行分割，没有匹配内容，则不会执行分割
    print(re.split('x', 'fkit, fkjava, crazyit'))
    # 输出：['fkit, fkjava, crazyit']
    ```

*   re.purge()：清除正则表达式缓存。
*   re.escape(pattern)：对模式中除 ASCII 字符、数值、下画线（_）之外的其他字符进行转义。如下程序示范了 escape() 函数的用法：

    ```
    import re
    # 对模式中特殊字符进行转义
    print(re.escape(r'www.crazyit.org is good, i love it!'))
    # 输出：www\.crazyit\.org\ is\ good\,\ i\ love\ it\!
    print(re.escape(r'A-Zand0-9?'))
    # 输出：A\-Zand0\-9\?
    ```

    从上面的输出结果可以看出，使用 escape() 函数对模式执行转义之后，模式中除 ASCII 字符、数值、下画线之外的其他字符都被添加了反斜线进行转义。

此外，在 re 模块中还包含两个类，它们分别是正则表达式对象（其具体类型为 _sre.SRE_Pattern）和匹配（Match）对象，其中正则表达式对象就是调用 re.compile() 函数的返回值。该对象的方法与前面介绍的 re 模块中的函数大致对应。

相比之下，正则表达式对象的 search()、match()、fullmatch()、findall()、finditer() 方法的功能更强大一些，因为这些方法都可额外指定 pos 和 endpos 两个参数，用于指定只处理目标字符串从 pos 开始到 endpos 结束之间的子串。如下程序示范了使用正则表达式的方法来执行匹配：

```
import re
# 编译得到正则表达式对象
pa = re.compile('fkit')
# 调用 match 方法，原本应该从开始匹配，
# 此处指定从索引为 4 的地方开始匹配，就可以成功匹配了
print(pa.match('www.fkit.org', 4).span()) # (4, 8)
# 此处指定从索引为 4 到索引 6 之间执行匹配，匹配失败
print(pa.match('www.fkit.org', 4, 6)) # None
# 此处指定从索引为 4 到索引 8 之间执行全匹配，匹配成功
print(pa.fullmatch('www.fkit.org', 4, 8).span()) # (4, 8)
```

上面程序示范了使用正则表达式调用 match()、fullmatch() 方法时指定 pos 和 endpos 参数的效果，在指定这两个参数之后，程序就可以只处理目标字符串的中间一段。此外，通过上面程序也可以体会到编译正则表达式的好处，即程序使用 compile() 函数编译正则表达式之后，该函数所返回的对象就会缓存该正则表达式，从而可以多次利用该正则表达式执行匹配。比如上面程序多次使用 pa 对象（它缓存了正则表达式）来执行匹配。

re 模块中的 Match 对象（其具体类型为 _sre.SRE_Match）则是 match()、search() 方法的返回值，该对象中包含了详细的正则表达式匹配信息，包括正则表达式匹配的位置、正则表达式所匹配的子串。

sre.SRE_Match 对象包含了如下方法或属性：

*   match.group([group1,...])：获取该匹配对象中指定组所匹配的字符串。
*   match.__getitem__(g)：这是 match.group(g) 的简化写法。由于 match 对象提供了 __getitem__() 方法，因此程序可使用 match[g] 来代替 match.group(g)。
*   match.groups(default=None)：返回 match 对象中所有组所匹配的字符串组成的元组。
*   match.groupdict(default= None)：返回 match 对象中所有组所匹配的字符串组成的字典。
*   match.start([group])：获取该匹配对象中指定组所匹配的字符串的开始位置。
*   match.end([group])：获取该匹配对象中指定组所匹配的宇符串的结束位置。
*   match.span([group])：获取该匹配对象中指定组所匹配的字符串的开始位置和结束位置。该方法相当于同时返回 start() 和 end() 方法的返回值。

    上面这些方法都涉及了组的概念，组是正则表达式中很常见的一个东西，用圆括号将多个表达式括起来形成组。如果正则表达式中没有圆括号，那么整个表达式就属于一个默认组。如下程序示范了正则表达式包含组的情形：

    ```
    import re
    # 在正则表达式中使用组
    m = re.search(r'(fkit).(org)', r"www.fkit.org is a good domain")
    print(m.group(0)) # fkit.org
    # 调用简化写法，底层是调用 m.__getitem__(0)
    print(m[0]) # fkit.org
    print(m.span(0)) # (4, 12)
    print(m.group(1)) # fkit
    # 调用简化写法，底层是调用 m.__getitem__(1)
    print(m[1]) # fkit
    print(m.span(1)) # (4, 8)
    print(m.group(2)) # org
    # 调用简化写法，底层是调用 m.__getitem__(2)
    print(m[2]) # org
    print(m.span(2)) # (9, 12)
    # 返回所有组所匹配的字符串组成的元组
    print(m.groups())
    ```

    上面程序中 search() 函数使用了一个正则表达式： r'(tkit).(org)'，在该正则表达式内包含两个组，即 (fkit) 和 (org)，因此程序可以依次获取 group(0)、group(1)、group(2) 的值，也就是依次获取整个正则表达式所匹配的子串、第一个组所匹配的子串和第二个组所匹配的子串；程序也可以依次获取 span(0)、span(1)、span(2) 的值，也就是依次获取整个正则表达式所匹配的子串的开始位置和结束位置、第一个组所匹配的子串的开始位置和结束位置、第二个组所匹配的子串的开始位置和结束位置。

    运行上面程序，可以看到如下输出结果：

    fkit.org
    fkit.org
    (4, 12)
    fkit
    fkit
    (4, 8)
    org
    org
    (9, 12)
    ('fkit', 'org')

    从该程序可以看出，只要正则表达式能匹配得到结果，则不管正则表达式是否包含组，group(0)、span(0) 总能获得内容，因为它们分别是获取整个正则表达式所匹配的子串，以及该子串的开始位置和结束位置。

    如果在正则表达式中为组指定了名字（用 ?P<名字> 为正则表达式的组指定名字），就可以调用 groupdict() 方法来获取所有组所匹配的字符串组成的宇典（其中组名作为字典的 key）。例如如下代码：

    ```
    # 正则表达式定义了 2 个组，并为组指定了名字
    m2 = re.search(r'(?P<prefix>fkit).(?P<suffix>org)',\
        r"www.fkit.org is a good domain")
    print(m2.groupdict()) # {'prefix': 'fkit', 'suffix': 'org'}
    ```

    上面程序为正则表达式的第一个组指定名字为 prefix，为第二个组指定名字为 suffix。运行上面程序，可以看到如下输出结果：

    {'prefix': 'fkit', 'suffix': 'org'}

    从上面的输出结果可以看到，此处返回的字典的 key 为正则表达式中的组名，value 为该组所匹配的子串。
*   match.pos：该属性返回传给正则表达式对象的 search()、match() 等方法的 pos 参数。
*   match.endpos：该属性返回传给正则表达式对象的 search()、match() 等方法的 endpos 参数。
*   match.lastindex：该属性返回最后一个匹配的捕获组的整数索引。如果没有组匹配，该属性返回 None。例如用 (a)b、((a)(b)) 或 ((ab)) 对字符串‘ab’执行匹配，该属性都会返回 1；但如果使用 (a)(b) 正则表达式对‘ab’执行匹配，则 lastindex 等于 2。
*   match.lastgroup：该属性返回最后一个匹配的捕获组的名字；如果该组没有名字或根本没有组匹配，该属性返回 None。
*   match.re：该属性返回执行正则表达式匹配时所用的正则表达式。
*   match.string：该属性返回执行正则表达式匹配时所用的字符串。