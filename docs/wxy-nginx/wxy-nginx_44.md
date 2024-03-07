# Nginx 伪流媒体服务器搭建

> 原文：[`www.weixueyuan.net/a/732.html`](http://www.weixueyuan.net/a/732.html)

Nginx 支持伪流媒体播放功能，其可以和客户端的 Flash 播放器结合，对以 .flv、.f4f、.mp4、.m4v、.m4a 为扩展名的文件实现流媒体的播放功能。若启用伪流媒体的支持功能，需要按媒体文件格式在配置编译时增加 --with-http_f4f_module、--with-http_flv_module 和 --with-http_mp4_module 这 3 个参数。

## 1、模块配置指令

伪流媒体模块配置指令如下表所示。

| 指令名称 | 指令值格式 | 默认值 | 指令说明 |
| f4f | -- | -- | 启用 F4F 文件支持 |
| f4f_buffer_size | size | 512k | 设置读取 .f4x 索引文件的缓冲区大小 |
| flv | -- | -- | 启用 FLV 文件支持 |
| mp4 | -- | -- | 启用 MP4 文件支持 |
| mp4_buffer_size | size | 512k | 设置处理 MP4 文件的缓冲区大小 |
| mp4_max_buffer_size | size | 512k | Metadata 数据处理过程中的最大缓冲区大小 |
| mp4_limit_rate | on 或 off 或 facto | off | 限制客户单媒体流请求的最大速率。仅在 Nginx 商业版本中提供 |
| mp4_limit_rate_after | time | 60s | 客户单媒体流请求的速率达到指定值时开始限速。仅在 Nginx 商业版本中提供 |

F4F 格式仅在 Nginx 商业版本中提供。

## 2、伪流媒体配置样例

伪流媒体配置样例是利用 Nginx 的自动索引功能生成 XML 格式的目录列表，通过 XSLT 生成前端页面，使用 jQuery 插件 video.js 的 Flash 播放器播放 FLV 及 MP4 格式的流媒体文件。页面效果如下图所示。

Nginx 配置样例如下：

```

server {
    listen 8081;
    server_name localhost;
    charset utf-8;
    root /opt/nginx-web/files;
    default_type text/xml;

    location / {
       autoindex on;                                    # 启用自动页面功能
       autoindex_localtime on;                          # 使用 Nginx 服务器时间
       autoindex_format xml;                            # 自动页面输出格式为 XML
       xslt_stylesheet conf/conf.d/example/test.xslt;   # 引入 XSLT 模板文件
    }

    location ~ \.flv$ {
        flv;                                            # FLV 文件启用伪流媒体支持
    }
    location ~ \.mp4$ {
        mp4;                                            # MP4 文件启用伪流媒体支持
        mp4_buffer_size       1m;                       # MP4 文件的缓冲区大小为 1MB
        mp4_max_buffer_size   5m;                       # MP4 文件最大缓冲区大小为 5MB
    }
}
```

文件 test.xslt 内容如下：

```

<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="/">
    <html>
    <head>
        <meta content="text/html; charset=UTF-8" http-equiv="Content-Type" />
        <link rel="stylesheet" href=”https://cdn.staticfile.org/twitter-bootstrap/ 3.3.7/css/bootstrap.min.css"/>
        <script src="https://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>
        <script src="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
        <link href="https://cdn.bootcss.com/video.js/6.6.2/video-js.css" ref= "stylesheet"/>
        <script src="https://cdn.bootcss.com/video.js/6.6.2/video.js"></script>
    </head>
    <body>
        <h3>Nginx 流媒体示例</h3>
        <table class="table table-striped table-bordered">
          <thead>
              <th>文件名</th>
              <th>文件类型</th>
              <th>文件大小</th>
              <th>文件修改时间</th>
          </thead>
          <xsl:for-each select="list/*">
            <xsl:sort select="mtime"/>

            <xsl:variable name="name">
                <xsl:value-of select="."/>
            </xsl:variable>
            <xsl:variable name="ext">
                <xsl:value-of select="substring($name,string-length($name)-2,3)"/>
            </xsl:variable>
            <xsl:variable name="size">
                <xsl:if test="string-length(@size) &gt; 0">
                        <xsl:if test="number(@size) &gt; 0">
                            <xsl:choose>
                                    <xsl:when test="round(@size div 1024) &lt; 1"> <xsl:value-of select="@size" /></xsl:when>
                                    <xsl:when test="round(@size div 1048576) &lt; 1"><xsl:value-of select="format-number((@size div 1024), '0.0')" />K</xsl:when>
                                    <xsl:otherwise><xsl:value-of select="format-number((@size div 1048576), '0.00')" />M</xsl:otherwise>
                            </xsl:choose>
                        </xsl:if>
                </xsl:if>
            </xsl:variable>
            <xsl:variable name="date">
                <xsl:value-of select="substring(@mtime,1,4)"/>-<xsl:value-of select= "substring(@mtime,6,2)"/>-<xsl:value-of select="substring(@mtime,9,2)"/><xsl:text> </xsl:text>
                <xsl:value-of select="substring(@mtime,12,2)"/>:<xsl:value-of select="substring(@mtime,15,2)"/>:<xsl:value-of select="substring(@mtime,18,2)"/>
            </xsl:variable>

          <tr>
              <td>
                <a href="{$name}"><xsl:value-of select="."/></a>
              </td>
              <td>
                <xsl:choose>
                  <xsl:when  test="$ext='mp4' or $ext='flv'">
                    <video id="example_video_1" class="video-js vjs-default-skin" controls="true" preload="none" width="640" height="264"  poster="http://vjs.zencdn.net/v/oceans.png">
                        <source src="{$name}" type="video/mp4"/>
                    </video>
                  </xsl:when>
                  <xsl:otherwise>
                    <xsl:value-of select="$ext"/>
                  </xsl:otherwise>
                  </xsl:otherwise>
                </xsl:choose>
              </td>
              <td align="center"><xsl:value-of select="$size"/></td>
              <td><xsl:value-of select="$date"/></td>
          </tr>
        </xsl:for-each>
      </table>
    </body>
    <script>
```