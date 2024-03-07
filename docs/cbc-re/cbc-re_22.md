# PHP 中使用正则表达式详解 preg_match() preg_replace() preg_mat

PHP 中嵌入正则表达式常用的函数有四个：

**1、preg_match() ：preg_match() 函数用于进行正则表达式匹配，成功返回 1 ，否则返回 0 。**

语法：int preg_match( string pattern, string subject [, array matches ] )

参数说明：

| 参数 | 说明 |
| pattern | 正则表达式 |
| subject | 需要匹配检索的对象 |
| matches | 可选，存储匹配结果的数组， $matches[0] 将包含与整个模式匹配的文本，$matches[1] 将包含与第一个捕获的括号中的子模式所匹配的文本，以此类推 |

例子 1 ：

```
<?php
   if(preg_match("/php/i", "PHP is the web scripting language of choice.", $matches))
   {
      print "A match was found:". $matches[0]; 
   } 
   else
   {
      print "A match was not found."; 
   } 
?> 
```

浏览器输出：

```
A match was found: PHP 
```

在该例子中，由于使用了 i 修正符，因此会不区分大小写去文本中匹配 php 。

提示：preg_match() 第一次匹配成功后就会停止匹配，如果要实现全部结果的匹配，即搜索到 subject 结尾处，则需使用 preg_match_all() 函数。

例子 2 ，从一个 URL 中取得主机域名 ：

```
<?php 
   // 从 URL 中取得主机名 
   preg_match("/^(http://)?([^/]+)/i","http://www.5idev.com/index.html", $matches); 
   $host = $matches[2]; // 从主机名中取得后面两段 
   preg_match("/[^./]+.[^./]+$/", $host, $matches); 
   echo "域名为：{$matches[0]}"; 
?> 
```

浏览器输出：

```
域名为：5idev.com 

```

**2、preg_match_all()：preg_match_all() 函数用于进行正则表达式全局匹配，成功返回整个模式匹配的次数（可能为零），如果出错返回 FALSE 。**

语法：int preg_match_all( string pattern, string subject, array matches [, int flags ] )

参数说明：

| 参数 | 说明 |
| pattern | 正则表达式 |
| subject | 需要匹配检索的对象 |
| matches | 存储匹配结果的数组 |
| flags | 可选，指定匹配结果放入 matches 中的顺序，可供选择的标记有： 

1.  PREG_PATTERN_ORDER：默认，对结果排序使 $matches[0] 为全部模式匹配的数组，$matches[1] 为第一个括号中的子模式所匹配的字符串组成的数组，以此类推
2.  PREG_SET_ORDER：对结果排序使 $matches[0] 为第一组匹配项的数组，$matches[1] 为第二组匹配项的数组，以此类推
3.  PREG_OFFSET_CAPTURE：如果设定本标记，对每个出现的匹配结果也同时返回其附属的字符串偏移量

 |

下面的例子演示了将文本中所有 <pre></pre> 标签内的关键字（php）显示为红色。

```
<?php 
   $str = "<pre>学习 php 是一件快乐的事。</pre><pre>所有的 phper 需要共同努力！</pre>"; 
   $kw = "php"; preg_match_all('/<pre>([sS]*?)</pre>/',$str,$mat); 
   for($i=0;$i<count($mat[0]);$i++)
   {
      $mat[0][$i] = $mat[1][$i]; 
      $mat[0][$i] = str_replace($kw, '<span style="color:#ff0000">'.$kw.'</span>', $mat[0][$i]); 
      $str = str_replace($mat[1][$i], $mat[0][$i], $str); 
   } 
   echo $str; 
?>

**3、preg_replace()：字符串比对解析并取代。**

语法: mixed preg_replace(mixed pattern, mixed replacement, mixed subject);

返回值: 混合类型资料

内容说明: 本函数以 pattern 的规则来解析比对字符串 subject，欲取而代之的字符串为参数 replacement。返回值为混合类型资料，为取代后的字符串结果。

范例：下例返回值为 $startDate = 6/19/1969

$patterns = array("/(19|20\d{2})-(\d{1,2})-(\d{1,2})/", "/^\s*{(\w+)}\s*=/");

$replace = array("\\3/\\4/\\1", "$\\1 =");

print preg_replace($patterns, $replace, "{startDate} = 1969-6-19");

**4、preg_split()：将字符串依指定的规则切开。**

语法: array preg_split(string pattern, string subject, int [limit]);

返回值: 数组

内容说明: 本函数可将字符串依指定的规则分开。切开后的返回值为数组变量。参数 pattern 为指定的规则字符串、参数 subject 则为待处理的字符串、参数 limit 可省略，表示欲处理的最多合乎值。
```