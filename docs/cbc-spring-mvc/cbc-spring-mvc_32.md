# Spring MVC 文件上传

> 原文：[`c.biancheng.net/view/4473.html`](http://c.biancheng.net/view/4473.html)

Spring MVC 框架的文件上传是基于 commons-fileupload 组件的文件上传，只不过 Spring MVC 框架在原有文件上传组件上做了进一步封装，简化了文件上传的代码实现，取消了不同上传组件上的编程差异。

## commons-fileupload 组件

由于 Spring MVC 框架的文件上传是基于 commons-fileupload 组件的文件上传，因此需要将 commons-fileupload 组件相关的 JAR（commons-fileupload-1.3.1.jar 和 commons-io-2.4.jar）复制到 Spring MVC 应用的 WEB-INF/lib 目录下。下面讲解如何下载相关 JAR 包。

Commons 是 Apache 开放源代码组织中的一个 Java 子项目，该项目包括文件上传、命令行处理、数据库连接池、XML 配置文件处理等模块。fileupload 就是其中用来处理基于表单的文件上传的子项目，commons-fileupload 组件性能优良，并支持任意大小文件的上传。

commons-fileupload 组件可以从“[`commons.apache.org/proper/commons-fileupload/`](http://commons.apache.org/proper/commons-fileupload/)”下载，本教程采用的版本是 1.2.2。下载它的 Binares 压缩包（commons-fileupload-1.3.1-bin.zip），解压缩后的目录中有两个子目录，分别是 lib 和 site。

在 lib 目录下有一个 JAR 文件——commons-fileupload-1.3.1.jar，该文件是 commons-fileupload 组件的类库。在 site 目录中是 commons-fileupload 组件的文档，也包括 API 文档。

commons-fileupload 组件依赖于 Apache 的另外一个项目——commons-io，该组件可以从“[`commons.apache.org/proper/commons-io/`](http://commons.apache.org/proper/commons-io/)”下载，本教程采用的版本是 2.4。下载它的 Binaries 压缩包（commons-io-2.4-bin.zip），解压缩后的目录中有 4 个 JAR 文件，其中有一个 commons-io-2.4.jar 文件，该文件是 commons-io 的类库。

## 基于表单的文件上传

标签 <input type="file"/> 会在浏览器中显示一个输入框和一个按钮，输入框可供用户填写本地文件的文件名和路径名，按钮可以让浏览器打开一个文件选择框供用户选择文件。

文件上传的表单例子如下：

<form method="post" action="upload" enctype="multipart/form-data">
    <input type="file" name="myfile"/>
</form>

对于基于表单的文件上传，不要忘记使用 enctype 属性，并将它的值设置为 multipart/form-data，同时将表单的提交方式设置为 post。为什么要这样呢？下面从 enctype 属性说起。

表单的 enctype 属性指定的是表单数据的编码方式，该属性有以下 3 个值。

*   application/x-www-form-urlencoded：这是默认的编码方式，它只处理表单域里的 value 属性值。
*   multipart/form-data：该编码方式以二进制流的方式来处理表单数据，并将文件域指定文件的内容封装到请求参数里。
*   text/plain：该编码方式只有当表单的 action 属性为“mailto：”URL 的形式时才使用，主要适用于直接通过表单发送邮件的方式。

由上面 3 个属性的解释可知，在基于表单上传文件时 enctype 的属性值应为 multipart/form-data。

## MultipartFile 接口

在 Spring MVC 框架中上传文件时将文件相关信息及操作封装到 MultipartFile 对象中，因此开发者只需要使用 MultipartFile 类型声明模型类的一个属性即可对被上传文件进行操作。该接口具有如下方法。

| 名称 | 作用 |
| byte[] getBytes() | 以字节数组的形式返回文件的内容 |
| String getContentType() | 返回文件的内容类型 |
| InputStream getInputStream() | 返回一个 InputStream，从中读取文件的内容 |
| String getName() | 返回请求参数的名称 |
| String getOriginalFillename() | 返回客户端提交的原始文件名称 |
| long getSize() | 返回文件的大小，单位为字节 |
| boolean isEmpty() | 判断被上传文件是否为空 |
| void transferTo(File destination) | 将上传文件保存到目标目录下 |

在上传文件时需要在配置文件中使用 Spring 的 org.springframework.web.multipart.commons.CommonsMultipartResolver 类配置 MultipartResolver 用于文件上传。

下面我们分两节介绍 Spring MVC 单文件上传和多文件上传：

*   Spring MVC 单文件上传（附带实例）
*   Spring MVC 多文件上传（附带实例）