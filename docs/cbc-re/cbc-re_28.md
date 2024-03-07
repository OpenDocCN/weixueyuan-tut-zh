# 正则表达式匹配 URL 或者网址

| 正则表达式 | (http&#124;ftp&#124;https):\/\/[\w\-_]+(\.[\w\-_]+)+([\w\-\.,@?^=%&amp;:/~\+#]*[\w\-\@?^=%&amp;/~\+#])? |
| 匹配 | http://regxlib.com/Default.aspx &#124; http://electronics.cnet.com/electronics/0-6342366-8-8994967-1.html |
| 不匹配 | www.yahoo.com |

| 正则表达式 | ^\\{2}[\w-]+\\(([\w-][\w-\s]*[\w-]+[$$]?$)&#124;([\w-][$$]?$)) |
| 匹配 | \\server\service &#124; \\server\my service &#124; \\serv_001\service$ |
| 不匹配 | \\my server\service &#124; \\server\ service &#124; \\server$\service |

| 正则表达式 | ^(http&#124;https&#124;ftp)\://([a-zA-Z0-9\.\-]+(\:[a-zA-Z0-9\.&amp;%\$\-]+)*@)?((25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[1-9])\.(25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[1-9]&#124;0)\.(25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[1-9]&#124;0)\.(25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[0-9])&#124;([a-zA-Z0-9\-]+\.)*[a-zA-Z0-9\-]+\.[a-zA-Z]{2,4})(\:[0-9]+)?(/[^/][a-zA-Z0-9\.\,\?\'\\/\+&amp;%\$#\=~_\-@]*)*$ |
| 匹配 | http://www.sysrage.net &#124; https://64.81.85.161/site/file.php?cow=moo's &#124;ftp://user:pass@host.com:123 |
| 不匹配 | sysrage.net |

| 正则表达式 | ^([a-zA-Z]\:&#124;\\\\[^\/\\:*?"<>&#124;]+\\[^\/\\:*?"<>&#124;]+)(\\[^\/\\:*?"<>&#124;]+)+(\.[^\/\\:*?"<>&#124;]+)$ |
| 匹配 | c:\Test.txt &#124; \\server\shared\Test.txt &#124; \\server\shared\Test.t |
| 不匹配 | c:\Test &#124; \\server\shared &#124; \\server\shared\Test.? |

| 正则表达式 | ^(http&#124;https&#124;ftp)\://([a-zA-Z0-9\.\-]+(\:[a-zA-Z0-9\.&amp;%\$\-]+)*@)*((25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[1-9])\.(25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[1-9]&#124;0)\.(25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[1-9]&#124;0)\.(25[0-5]&#124;2[0-4][0-9]&#124;[0-1]{1}[0-9]{2}&#124;[1-9]{1}[0-9]{1}&#124;[0-9])&#124;localhost&#124;([a-zA-Z0-9\-]+\.)*[a-zA-Z0-9\-]+\.(com&#124;edu&#124;gov&#124;int&#124;mil&#124;net&#124;org&#124;biz&#124;arpa&#124;info&#124;name&#124;pro&#124;aero&#124;coop&#124;museum&#124;[a-zA-Z]{2}))(\:[0-9]+)*(/($&#124;[a-zA-Z0-9\.\,\?\'\\\+&amp;%\$#\=~_\-]+))*$ |
| 匹配 | http://site.com/dir/file.php?var=moo &#124; https://localhost &#124;ftp://user:pass@site.com:21/file/dir |
| 不匹配 | site.com &#124; http://site.com/dir// |

| 正则表达式 | ^([a-zA-Z]\:)(\\[^\\/:*?<>"&#124;]*(?<![ ]))*(\.[a-zA-Z]{2,6})$ |
| 匹配 | C:\di___r\fi_sysle.txt &#124; c:\dir\filename.txt |
| 不匹配 | c:\dir\file?name.txt |

| 正则表达式 | ^(a-zA-Z0-9?\.)+[a-zA-Z]{2,6}$ |
| 匹配 | regexlib.com &#124; this.is.a.museum &#124; 3com.com |
| 不匹配 | notadomain-.com &#124; helloworld.c &#124; .oops.org |

| 正则表达式 | ^(((ht&#124;f)tp(s?))\://)?(www.&#124;[a-zA-Z].)[a-zA-Z0-9\-\.]+\.(com&#124;edu&#124;gov&#124;mil&#124;net&#124;org&#124;biz&#124;info&#124;name&#124;museum&#124;us&#124;ca&#124;uk)(\:[0-9]+)*(/($&#124;[a-zA-Z0-9\.\,\;\?\'\\\+&amp;%\$#\=~_\-]+))*$ |
| 匹配 | www.blah.com:8103 &#124; www.blah.com/blah.asp?sort=ASC &#124;www.blah.com/blah.htm#blah |
| 不匹配 | www.state.ga &#124; http://www.blah.ru |

| 正则表达式 | \b(([\w-]+://?&#124;www[.])[^\s()<>]+(?:\([\w\d]+\)&#124;([^[:punct:]\s]&#124;/))) |
| 匹配 | http://foo.com/blah_blah &#124; http://foo.com/blah_blah/ &#124; (Something like http://foo.com/blah_blah) &#124; http://foo.com/blah_blah_(wikipedia) &#124; (Something like http://foo.com/blah_blah_(wikipedia)) &#124; http://foo.com/blah_blah. &#124;http://foo.com/blah_blah/. &#124; <http://foo.com/blah_blah> &#124; <http://foo.com/blah_blah/>&#124; http://foo.com/blah_blah, &#124; http://www.example.com/wpstyle/?p=364. &#124; http://?df.ws/123 &#124; rdar://1234 &#124; rdar:/1234 &#124; http://userid:password@example.com:8080 &#124;http://userid@example.com &#124; http://userid@example.com:8080 &#124;http://userid:password@example.com |
| 不匹配 | no_ws.example.com &#124; no_proto_or_ws.com &#124; /relative_resource.php |