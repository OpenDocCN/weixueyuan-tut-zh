# JDK 中的 XML API

JDK 中涉及 XML 的 API 有两个，分别是：

1.  The Java API For XML Processing：负责解析 XML。
2.  Java Architecture for XML Binding：负责将 XML 映射为 Java 对象。

它们所涉及的类包有 javax.xml.*、org.w3c.dom*、org.xml.sax.* 和 javax.xml.bind.*。

其中经常用到的类如表 1 所示。

表 1 常用的 JDK XML API 类

| 类 | 说明 |
| javax.xml.parsers.DocumentBuilder | 从 XML 文档获取 DOM 文档实例 |
| javax.xml.parsers.DocumentBuilderFactory | 从 XML 文档获取生成 DOM 对象的解析器 |
| javax.xml.parsers.SAXParser | 获取基于 SAX 的解析器 XML 文档实例 |
| javax.xml.parsers.SAXParserFactory | 获取基于 SAX 的解析器以解析 XML 文档 |
| org.w3c.dom.Document | 整个 XML 文档 |
| org.w3c.dom.Element | XML 文档的一个元素 |
| org.w3c.dom.Node | Node 接口是整个文档对象模型的主要数据类型 |
| org.xml.sax.XMLReader | XML 解析器是 SAX2 的驱动程序必须实现的接口 |