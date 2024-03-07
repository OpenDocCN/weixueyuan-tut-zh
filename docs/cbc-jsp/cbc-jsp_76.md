# SAX 解析 XML

SAX（Simple API for XML）也是一种解析 XML 文件的方法，它虽然不是官方标准，但它是 XML 的事实标准，大部分 XML 解析器都支持它。

SAX 与 DOM 不同的是，它不是一次性将 XML 加载到内存中，而是从 XML 文件的开始位置进行解析，根据定义好的事件处理器，来决定当前解析的部分是否有必要存储。下面例子说明了 SAX 解析 XML 的过程。

【例 1】SAX 解析 XML 文件。将《DOM 解析 XML》中例 1 的 XML 文件作为源文件，编写解析类 JAXBSAXDemo.java，代码如下：

```
package com.eshore;
import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.XMLReader;
import org.xml.sax.helpers.DefaultHandler;
import org.xml.sax.helpers.XMLReaderFactory;
public class JAXBSAXDemo extends DefaultHandler
{
    private String preTag;
    //接收文档开始的通知
    @Override
    public void startDocument() throws SAXException
    {
        preTag=null;
    }
    //接收元素开始的通知
    @Override
    public void startElement(String uri, String localName,String qName,Attributes attributes)throws SAXException
    {
        if("user".equals(qName))
        {
            System.out.println(qName+"节点的 country 属性值为："+attributes.getValue("country"));
        }
        if("address".equals(qName))
        {
            System.out.println(qName+"节点的 state 属性值为："+attributes.getValue("state"));
        }
        preTag=qName;
    }  
    //接收元素结束的通知
    @Override
    public void endElement(String uri,String localName, String qName)throws SAXException
    {
        preTag=null;
    }
    //接收元素中数据的通知，在执行完 startElement 和 endElement 方法之后执行
    public void characters(char ch[], int start, int length)throws SAXException
    {
        String value=new String(ch, start, length);
        if("name".equals(preTag))
        {
            System.out.println("name 节点的值为："+value);
        }
        else if("english_name".equals(preTag))
        {
            System.out.println("english_name 节点的值为："+value);
        }
        else if("age".equals(preTag))
        {
            System.out.println("age 节点的值为："+value);
        }
        else if("sex".equals(preTag))
        {
            System.out.println("sex 节点的值为："+value);
        }
        else if("description".equals(preTag))
        {
            System.out.println("description 节点的值为："+value);
        }
        if("city".equals(preTag))
        {
            System.out.println("city 节点的值为："+value);
        }
        else if("area".equals(preTag))
        {
            System.out.println("area 节点的值为："+value);
        }
    }
    public static void main(String[] args) throws Exception
    {
        //由 XMLReaderFactory 类 创建 XMLReader 实例
        XMLReader xmlReader=XMLReaderFactory.createXMLReader();
        //创建一事件监听类
        JAXBSAXDemo handler=new JAXBSAXDemo();
        //XMLReader 解析类设定事件处理类
        xmlReader.setContentHandler(handler);
        //XMLReader 解析类解析 XML 文件
        xmlReader.parse("E:\\users.xml");
    }
}
```

上述代码中介绍了用 SAX 解析 XML 文件的步骤。通过上述代码可以看出，使用 SAX 解析 XML 时，需要以下几个步骤：

*   用 XMLReaderFactory 类创建 XMLReader 实例：

XMLReader xml Reader=XMLReaderFactory. createXMLReader();

*   创建一个事件监听类：

JAXBSAXDemo handler=new JAXBSAXDemo();

*   为解析类设定事件处理类：

xmlReader.setContentHandler(handler);

*   解析 XML 文件：

xmlReader. parse ("E:\\users.xml");

提示：上述实例中应用的是 XMLReader 而不是 SAXParser，是因为在 SAX2 中实现解析的接口名称重命名为 XMLReader。在使用 SAX 解析 XML 资源文件时，默认使用 SAXParser 实现类，它继承自 AbstractSAXParser。同理，工厂类也是使用 XMLReaderFactory 而不只 ISASParserFactory 来创建解析类。