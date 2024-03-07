# Nginx 文件下载服务器搭建

> 原文：[`www.weixueyuan.net/a/705.html`](http://www.weixueyuan.net/a/705.html)

在对外分享文件时，利用 Nginx 搭建一个简单的下载文件管理服务器，文件分享就会变得非常方便。利用 Nginx 的诸多内置指令可实现自动生成下载文件列表页、限制下载带宽等功能。配置样例如下：

```

server {
    listen 8080;
    server_name  localhost;
    charset utf-8;
    root    /opt/nginx-web/files;             # 文件存放目录

    # 下载
    location / {
        autoindex on;                         # 启用自动首页功能
        autoindex_format html;                # 首页格式为 HTML
        autoindex_exact_size off;             # 文件大小自动换算
        autoindex_localtime on;               # 按照服务器时间显示文件时间

        default_type application/octet-stream;# 将当前目录中所有文件的默认 MIME 类型设置为
                                              # application/octet-stream

        if ($request_filename ~* ^.*?\.(txt|doc|pdf|rar|gz|zip|docx|exe|xlsx|ppt|pptx)$){
            # 当文件格式为上述格式时，将头字段属性 Content-Disposition 的值设置为"attachment"
            add_header Content-Disposition: 'attachment;'; 
        }
        sendfile on;                          # 开启零复制文件传输功能
        sendfile_max_chunk 1m;                # 每个 sendfile 调用的最大传输量为 1MB
        tcp_nopush on;                        # 启用最小传输限制功能

        aio on;                               # 启用异步传输
        directio 5m;                          # 当文件大于 5MB 时以直接读取磁盘的方式读取文件
        directio_alignment 4096;              # 与磁盘的文件系统对齐
        output_buffers 4 32k;                 # 文件输出的缓冲区大小为 128KB

        limit_rate 1m;                        # 限制下载速度为 1MB
        limit_rate_after 2m;                  # 当客户端下载速度达到 2MB 时进入限速模式
        max_ranges 4096;                      # 客户端执行范围读取的最大值是 4096B
        send_timeout 20s;                     # 客户端引发传输超时时间为 20s
        postpone_output 2048;                 # 当缓冲区的数据达到 2048B 时再向客户端发送
        chunked_transfer_encoding on;         # 启用分块传输标识
    }
}
```