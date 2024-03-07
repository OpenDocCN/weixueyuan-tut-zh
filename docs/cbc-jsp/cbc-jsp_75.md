# DOM 解析 XML

XML 结构是一种树型结构，处理步骤都差不多，Java 己经将它们封装成了现成的类库。目前流行的解析方法有三种，分别为 DOM、SAS 和 DOM4j。

DOM（Document Object Model，文档对象模型）是 W3C 组织推荐的处理 XML 的一种方式。它是一种基于对象的 API，把 XML 的内容加载到内存中，生成一个 XML 文档相对应的对象模型，根据对象模型，以树节点的方式对文档进行操作。下面用实例说明解析步骤。

【例 1】DOM 解析 XML 文件。
假设 user.xml 文件如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<users>
<user country="中国">
<name>白真</name>
<english_name>baizhen</english_name>
<age>32</age>
<sex>男</sex>
<address state="北京">
    <city>北京市</city>
    <area>朝阳区</area>
</address>
<description>他是一个大学老师</description>
</user>
<user country="中国">
<name>李华华</name>
<english_name>lihuahua</english_name>
<age>30</age>
<sex>女</sex>
<address state="河北省">
    <city>石家庄市</city>
    <area>裕华区</area>
</address>
<description>她是一个律师</description>
</user>
</users>
```

编写解析类 JAXBDomDemo.java 的代码如下：

```
package com.eshore;
import java.io.File;
import java.io.IOException;
import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;
public class JAXBDomDemo
{
    /**
    *用 dom 解析 XML 文件
    */
    public static void main(String[] args)
    {
        //创建待解析的 XML 文件，并指定目标
        File file=new File("F:\\users.xml");
        //用单例模式创建 DocumentBuilderFactory 对象
        DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
        //声明一个 DocumentBuilder 对象
        DocumentBuilder documentBuilder =null;
        try
        {
            //通过 DocumentBuilderFactory 构建 DocumentBuilder 对象
            documentBuilder=factory.newDocumentBuilder();
            //DocumentBuilder 解析 xml 文件
            Document document=documentBuilder.parse(file);
            //获得 xml 文档中的元素根元素
            Element root=document.getDocumentElement();
            //输出根元素的名称
            System.out.println("根元素："+root.getNodeName());
            //获得根元素下的子节点
            NodeList childNodes = root.getChildNodes();
            //遍历根元素下的子节点
            for(int i=0;i<childNodes.getLength();i++)
            {
                //获得根元素下的子节点
                Node node=childNodes.item(i);
                System.out.println("节点的名称为"+node.getNodeName());
                //获得子节点的 country 属性值
                String attributeV=node.getAttributes().getNamedItem("country").getNodeValue();
                System.out.println(node.getNodeName()+"节点的 country 属性值为"+attributeV);
                //获得 node 子节点下集合
                NodeList nodeChilds=node.getChildNodes();
                //遍历 node 子节点下集合
                for(int j=0;j<nodeChilds.getLength();j++)
                {
                    Node details=nodeChilds.item(j);
                    String name=details.getNodeName();
                    //判断如果是 address 元素，获取子节点
                    if("address".equals(name))
                    {
                        NodeList addressNodes=details.getChildNodes();
                        //遍历 address 元素子节点
                        for(int k=0;k<addressNodes.getLength();k++)
                        {
                            Node addressDetail=addressNodes.item(k);
                            System.out.println(node.getNodeName()+"节点的子节点"+name+"点的子节点"+
                            addressDetail.getNodeName()+" 节点内容为："+addressDetail.getTextContent());
                        }
                        String addressAtt=details.getAttributes().getNamedItem("state").getNodeValue();
                        System.out.println(name+"节点的 state 属性值为"+addressAtt);
                    }
                    System.out.println(node.getNodeName()+"节点的子节点"+details.getNodeName()+
                             "节点的内容为:"+details.getTextContent());
                }
            }
        }
        catch(ParserConfigurationException e)
        {
            e.printStackTrace();
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        catch (SAXException e)
        {
            e.printStackTrace();
        }
    }
}
```

上述代码详细地描述了解析步骤。通过上述代码，不难发现 DOM 解析 XML 时，主要有以下几步：

*   创建 DocumentBuilderFactory 对象：

//用单例模式创建 DocumentBuilderFactory 对象
DocumentBuilderFactory factory=DocumentBuilderFactory.newlnstance();

*   通过 DocumentBuilderFactory 构建 DocumentBuilder 对象：

DocumentBuilder documentBuilder=factory.newDocumentBuilder();

*   DocumentBuilder 解析 xml 文件变为 Document 对象：

Document document=documentBuilder.parse(file);

取得 Document 对象之后就可以用 Document 中的方法获取 XML 数据了。