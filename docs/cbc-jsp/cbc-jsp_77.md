# DOM4j 解析 XML

DOM 在解析的时候是把整个 XML 文件映射到 Document 的树型结构中，XML 中的元素、属性、文本都能在 Document 中看清，但是它消耗内存，查询速度慢。

SAX 基于事件的解析，解析器在读取 XML 时根据读取的数据产生相应的事件，由应用程序实现相应的事件处理，所以它的解析速度快，内存占用少。但是 SAX 需要应用程序自身处理解析器的状态，实现起来比较麻烦，而且它只支持对 XML 文件的读取，不支持写入。

DOM4j 是一个简单、灵活的开源库，前身是 JDOM。与 JDOM 所不同的是，DOM4j 使用接口和抽象类基本方法，用了大量的 Collections 类，提供一些替代方法以允许更好的性能或更直接的编码方法。DOM4j 不仅可以读取 XML 文件，而且还可以写入 XML 文件。

目前越来越多的 Java 软件都在使用 DOM4j 读写 XML，例如 Hibernate，包括 Sun 公司自己的 JAXM 也使用 DOM4j。可以通过下载地址 [`sourceforge.net/projects/dom4j`](https://sourceforge.net/projects/dom4j/) 下载最新的 D0M4j 包。下面用例子来说明其使用方法。

【例 1】DOM4j 解析 XML 文件。

XML 文件的内容还是上述中的内容，编写解析类 Dom4jDemo.java，其代码如下：

```
package com.eshore;
import java.io.File;
import java.util.List;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
public class Dom4jDemo
{
    /**
     * Dom4j 解析 XML 文件
     */
    @SuppressWarnings("unchecked")
    public static void main(String[] args)
    {
        //创建待解析的 XML 文件，并指定目录
        File file=new File("F:\\users.xml");
        //指定 XML 解析器 SAXReader
        SAXReader saxReader=new SAXReader();
        try
        {
            //SAXReader 解析 XML 文件
            Document document=saxReader.read(file);
            //指定要解析的节点
            List<Element> list=document.selectNodes("/users/user");
            for(Element element:list)
            {
                //获得节点 country 属性值
                System.out.println("country----"+element.attributeValue("country"));;
                //获得节点的子节点
                List<Element> childList = element.elements();
                //遍历节点的子节点
                for(Element childelement:childList)
                {
                    //如果是 address 子节点，遍历 address 的子元素
                    if("address".equals(childelement.getName()))
                    {
                        //获得节点 state 属性值
                        System.out.println("state----"+childelement.attributeValue("state"));
                        //遍历 address 元素的子元素
                        List<Element> addressElements=childelement.elements();
                        for(Element e:addressElements)
                        {
                            System.out.println(e.getName()+"----"+e.getText());
                        }
                    }
                    System.out.println(childelement.getName()+"----"+childelement.getText());
                }
            }
        }
        catch(DocumentException e)
        {
            e.printStackTrace();
        }
    }
}
```

上述代码讲解了用 D0M4j 来解析 XML 资源文件。通过上述代码可知，使用 D0M4j 解析 XML 时，主要有以下几步：

*   创建 SAXReader 实例：

SAXReader saxReader=new SAXReader();

*   用 SAXReader 获取 xml 的 Document：

Document document=saxReader.read(file);

从上述步骤可以看出，用 DOM4j 解析 XML 便于使用。想要了解更多的 DOM4j 内容，请查询 DOM4j 的 API 和相关例子。