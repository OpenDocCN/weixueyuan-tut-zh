# 正则表达式匹配 HTML 标签或标记

| 正则表达式 | <(\S*?) [^>]*>.*?</\1>&#124;<.*? /> |
| 匹配 | <html>hello</html>&#124;<a>abcd</a> |
| 不匹配 | abc&#124;123&#124;<html>ddd |

| 正则表达式 | ^[^<>`~!/@\#}$%:;)(_^{&*=&#124;'+]+$ |
| 匹配 | This is a test |
| 不匹配 | <href = &#124; <br> &#124; That's it |

| 正则表达式 | &lt;!--.*?--&gt; |
| 匹配 | &lt;!-- &lt;h1&gt;this text has been removed&lt;/h1&gt; --&gt; &#124; &lt;!-- yada --&gt; |
| 不匹配 | &lt;h1&gt;this text has not been removed&lt;/h1&gt; |

| 正则表达式 | (\[(\w+)\s*(([\w]*)=('&#124;&quot;)?([a-zA-Z0-9&#124;:&#124;\/&#124;=&#124;-&#124;.&#124;\?&#124;&amp;]*)(\5)?)*\])([a-zA-Z0-9&#124;:&#124;\/&#124;=&#124;-&#124;.&#124;\?&#124;&amp;&#124;\s]+)(\[\/\2\]) |
| 匹配 | [link url=&quot;http://www.domain.com/file.extension?getvar=value&amp;secondvar=value&quot;]Link[/li |
| 不匹配 | [a]whatever[/b] &#124; [a var1=something var2=somethingelse]whatever[/a] &#124; [a]whatever[a] |

| 正则表达式 | href=\"\'?\w+(\.\w+)*(\/\w+(\.\w+)?)*(\/&#124;\?\w*=\w*(&\w*=\w*)*)?[\"\'] |
| 匹配 | href="www.yahoo.com" &#124; href="http://localhost/blah/" &#124; href="eek" |
| 不匹配 | href="" &#124; href=eek &#124; href="bad example" |

| 正则表达式 | &quot;(^&quot;*)&quot; |
| 匹配 | &quot;This is a \&quot;string\&quot;.&quot; |
| 不匹配 | &quot;This is a \&quot;string\&quot;. |

| 正则表达式 | (?i:on(blur&#124;c(hange&#124;lick)&#124;dblclick&#124;focus&#124;keypress&#124;(key&#124;mouse)(down&#124;up)&#124;(un)?load&#124;mouse(move&#124;o(ut&#124;ver))&#124;reset&#124;s(elect&#124;ubmit))) |
| 匹配 |  

&#124;   &#124; onclick &#124; onsubmit &#124; onmouseover &#124;

 |
| 不匹配 | click &#124; onandon &#124; mickeymouse |

| 正则表达式 | (?s)/\*.*\*/ |
| 匹配 | /* .................... */ &#124; /* imagine lots of lines here */ |
| 不匹配 | */ malformed opening tag */ &#124; /* malformed closing tag /* |

| 正则表达式 | <(\S*?) [^>]*>.*?</\1>&#124;<.*? /> |
| 匹配 | <html>hello</html>&#124;<a>abcd</a> |
| 不匹配 | abc&#124;123&#124;<html>ddd |

| 正则表达式 | \xA9 |
| 匹配 | © |
| 不匹配 | anything |

| 正则表达式 | src[^&gt;]*[^/].(?:jpg&#124;bmp&#124;gif)(?:\&quot;&#124;\') |
| 匹配 | src=&quot;../images/image.jpg&quot; &#124; src=&quot;http://domain.com/images/image.jpg&quot; &#124; src='d:\w |
| 不匹配 | src=&quot;../images/image.tif&quot; &#124; src=&quot;cid:value&quot; |

| 正则表达式 | /\*[\d\D]*?\*/ |
| 匹配 | /* my comment */ &#124; /* my multiline comment */ &#124; /* my nested comment */ |
| 不匹配 | */ anything here /* &#124; anything between 2 seperate comments &#124; \* *\ |

| 正则表达式 | <[a-zA-Z]+(\s+[a-zA-Z]+\s*=\s*("([^"]*)"&#124;'([^']*)'))*\s*/> |
| 匹配 | <img src="test.gif"/> |
| 不匹配 | <img src="test.gif"> &#124; <img src="test.gif"a/> |