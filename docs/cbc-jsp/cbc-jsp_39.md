# JavaBean 事件

事件处理是 JavaBean 体系结构的核心之一。通过事件处理机制，可让一些组件作为事件源，发出可被描述环境或其他组件接收的事件。这样，不同的组件就可在构造工具内组合在一起，组件之间通过事件的传递进行通信，构成一个应用。

从概念上讲，事件是一种在“源对象”和“监听者对象”之间，某种状态发生变化的传递机制。事件有许多不同的用途，例如，在 Windows 系统中常要处理的鼠标事件、窗口边界改变事件、键盘事件等。

在 Java 和 JavaBean 中则是定义了一个一般的、可扩充的事件机制，这种机制能够：

1.  对事件类型和传递的模型的定义和扩充提供一个公共框架，并适合于广泛的应用。
2.  与 Java 语言和环境有较高的集成度。
3.  事件能被描述环境捕获。
4.  能使其他构造工具釆取某种技术在设计时直接控制事件，以及事件源和事件监听者之间的联系。
5.  事件机制本身不依赖于复杂的开发工具。
6.  能够发现指定的对象类可以生成的事件。
7.  能够发现指定的对象类可以观察（监听）到的事件。
8.  提供一个常规的注册机制，允许动态操纵事件源与事件监听者之间的关系。
9.  不需要其他的虚拟机和语言即可实现。
10.  事件源与监听者之间可进行高效的事件传递。
11.  能完成 JavaBean 事件模型与其他相关组件体系结构事件模型的中立映射。

JavaBean 事件从事件源到监听者的传递是通过对目标监听者对象的 Java 方法调用进行的。对每个明确发生的事件，都相应地定义一个明确的 Java 方法。这些方法都集中在事件监听者（EventListener）接口中定义，这个接口要继承 java.util.EventListener。实现了事件监听者接口中一些或全部方法的类就是事件监听者。

伴随着事件的发生，相应的状态通常都封装在事件状态对象中，该对象必须继承自 java.util.EventObject。事件状态对象作为单参传递给应响应该事件的监听者方法。

发出某种特定事件的事件源的标识是：遵从规定的设计格式为事件监听者定义注册方法，并接受对指定事件监听者接口实例的引用。有时，事件监 听者不能直接实现事件监听者接口，或者还有其他的额外动作时，就要在一个源与其他一个或多个监听者之间插入一个事件适配器类的实例，来建立它们之间的联系。

与事件发生有关的状态信息一般都封装在一个事件状态对象（Event State Object）中，这种对象是 java.util.EventObject 的子类。按设计习惯，这种事件状态对象类的名应以 Event 结尾。例如：

```
public class MouseMovadExampleEvent extends java.util.EventObject
{
    protected int x,y;
    //创建一个鼠标移动事件 MbuseMovedExampleEvent
    MouseMovedExampleEvent(java.awt.Component source,Point location)
    {
        super(source);
        x=location.x;
        y=location.y;
    }
    //获取鼠标位置
    public Point getLocation()
    {
        return new Point(x,y);
    }
}
```

## 事件监听者接口（EverrtListener Interface）与事件监听者

由于 Java 事件模型均基于方法调用，因而需要一个定义并组织事件操纵方法的方式。在 JavaBean 中，事件操纵方法都被定义在继承了 java.util.EventListener 类的 EventListener 接口中。

按规定，EventListener 接口的命名要以 Listener 结尾。任何一个类如果想操纵在 EventListener 接口中定义的方法都必须以实现这个接口的方式进行。这个类也就是事件监听者。例如：

//先定义一个鼠标移动事件象
public class MouseMovedExampleEvent extends java.util.EventObject
{
    //在此类中包含与鼠标移动拿件有关的状态信息
}
//定义鼠标移动事件的监听者接口
interface MouseMovedExampleListener extends java.util.EventListener
{
    //在这个接口中定义鼠标移 f 事件监听者所应支持的方法
    void mouseMoved(MouseMovedExampleEvent mine);
}

在接口中只定义方法名、方法的参数和返回值类型。例如，上面接口中的 mouseMoved 方法的具体实现是在下面的 ArbitraryObject 类中定义的。

class ArbitraryObject implements MouseMovedExampleListener
{
    public void mouseMoved(MouseMovedExampleEvent mine){…}
}

ArbitraryObject 就是 MouseMovedExampleEvent 事件的监听者。

## 事件监听者的注册与注销

事件监听者把自己注册入合适的事件源中，建立源与事件监听者间的事件流，事件源必须为事件监听者提供注册和注销的方法。在前面的 bound 属性介绍中已看到了这种使用过程。在实际中，事件监听者的注册和注销要使用标准的设计格式：

public void add<ListenerType>(<ListenerType> listener);
public void remove<ListenerType>(<ListenerTypp> listener);

首先定义了一个事件监听者接口：

```
public interface ModelChangedListener extends java.util.EventListener
{
    void modelChanged(EventObject e);
}
```

接着定义事件源类：

```
public abstract class Model
{
    private Vector listeners=new Vector();    //定了一个储存事件监听者的数组
    //上面设计格式中的 <ListenerType> 在此处即是下面的 ModqkchangedListener
    public synchronized void addModelChangedListener(ModelChangedListener mc1)
    {
        listeners.addElement(mc1);
    }
    //把监听者注册入 listeners 数组中
    public synchronized void removeModelChangedListener(ModelChangedListener mc1)
    {
        listeners.removeElement(mc1);    //把监听者从 listeners 中注销
    }
    //以上两个方法的前面均冠以 synchronized，是因为运行在多线程环境时，可能有几个对象同时要进行注册和注销操作，要使用 synchronized 来确保它们之间的同步。开发工具或程序员使用这两个方法建立源与监听者之间的事件流
    protected void notifyModelChanged()
    {
        //事件源使用本方法通知监听者发生了 modelChanged 事件
        Vector l;
        EventObject e=new EventObject(this);
        //首先要把监听者拷贝到数组 l 中，冻结 EventListeners 的状态以传递事件。这样来确保在事件传递到所有监听者之前，己接收了事件的目标监听者的对应方法暂不生效
        synchronized(this)
        {
            l=(Vector)listeners.clone();
        }
        for(int i=0;i<l.size();i++)
        {
            //依次通知在监听者队列中注册的每个监听者发生了 modelChiiged 事件，并把事件状态对象 e 作为参数传递给监听者队列中的每个监听者
            ((ModelChangedListener)l.elementAt(i)).modelChahged(e);
        }
    }
}
```

在程序中可见，事件源 Model 类显式地调用了接口中的 modelChanged 方法，实际是把事件状态对象 e 作为参数，传递给了监听者类中的 modelClianged 方法。

## 适配类

适配类是 Java 事件模型中极其重要的一部分。在一些应用场合，事件从源到监听者之间的传递要通过适配类来“转发”。例如，当事件源发出一个事件，而有几个事件监听者对象都可接收该事件，但只需要指定对象做出反应时，就要在事件源与事件监听者之间插入一个事件适配器类，由适配器类来指定事件应该由哪些监听者来响应。

适配类成为了事件监听者，事件源实际是把适配类作为监听者注册入监听者队列中，而真正的事件响应者并未在监听者队列中，事件响应者应做的动作由适配类决定。目前绝大多数的开发工具在生成代码时，事件处理都是通过适配类来进行的。

## JavaBean 用户化

JavaBean 开发者可以给 bean 添加用户化器（Customizer）、属性编辑器（PropertyEditor）和 BeanInfo 接口来描述 bean 的内容。bean 的使用者可在构造环境中通过与 bean 附带在一起的这些信息来用户化 bean 的外观和应做的动作。

bean 不必都有 BeansCustomizer、PropertyEditor 和 BeanInfo，根据实际情况，这些是可选的。当有些 bean 较复杂时，就要提供这些信息，以 Wizard 的方式使 bean 的使用者能够用户化 bean。有些简单的 bean 可能这些信息都没有，则构造工具可使用自带的透视装置，透视出 bean 的内容，并把信息显示到标准的属性表或事件表中供使用者用户化 bean。

前几节提到的 bean 的属性、方法和事件名要以一定的格式命名，主要的作用就是供开发工具对 bean 进行透视。当然也是给程序员在手写程序中使用 bean 提供方便，使它能观其名、知其意。

#### 1\. 用户化器接口（Customizer Interface）

当一个 bean 有了自己的用户化器时，在构造工具内就可展现出自己的属性表。在定义用户化器时必须要实现 java.Bean.Customizer 接口。例如，一个“按钮”bean 的方法，代码如下：

```
public class OurButtonCustomizer extends Panel implements Customizer
{
    //当实现类似 OurButtonCustomizer 这样的常规属性表时，一定要在其中实现 addProperChangeListener 和 removePropertyChangeListener，这样构造工具可用这些功能代码为属性事件添加监听者
    private PropertyChangeSupport changes=new PropertyChangeSupport(this);
    public void addPropertyChangeListener(PropertyChangeListener l)
    {
        changes.addPropertyChangeListener(l);
        public void removeProgertyChangeListener(PropertyChangeListener l)
        {
            changes.removePropertyChangeListener(l);
        }
    }
}
```

#### 2\. 属性编辑器接口（PropertyEditor Interface）

JavaBean 可提供 PropertyEditor 类，为指定的属性创建一个编辑器。这个类必须继承自 java.Bean.PropertyEditorSuppon 类。构造工具与手写代码的程序员不直接使用这个类，而是在下一小节的 BeanInfo 中实例化并调用这个类，如下：

```
public class MoleculeNameEditor extends java.Bean.PropertyEditorSupport
{
    public String[] getTags()
    {
        String resule[]={"HyaluronicAcid","Benzene","buckmisterfullerine","cyclohexane","ethane","water"
    };
    return resule;
}
```

上例中是为 Tags 属性创建了属性编辑器，在构造工具内，可从下拉表格中选择 MoleculeName 的属性，应是 Hyaluronic Acid 或 water。

#### 3\. BeanInfo 接口

每个 bean 类也可能有与之相关的 BeanInfo 类，在其中描述了这个 bean 在构造工具内出现时的外观。BeanInfo 中可定义属性、方法、事件，显示它们的名称，提供简单的帮助说明，如下：

```
public class MoleculeBeanInfo extends SimpleBeanInfo
{
    public PropertyDescriptor[] getPropertyDescriptors()
    {
        try
        {
            PropertyDescriptor pd=new PropertyDescriptor("moleculeName",Molecule.class);
            //通过 pd 引用上一节的 MoleculeNameEditor 类，取得并返回 moleculeName 属性
            pd.setPropertyEditorClass(MoleculeNameEditor.class);
            PropertyDescriptor result[]={pd};
            return result;
        }
        catch(Exception ex)
        {
            System.err.println("MoleculeBeanInfo:unexpected exeption:"+ex);
            return null;
        }
    }
}
```

## JavaBean 持久化

当 JavaBean 在构造工具内被用户化，并与其他 bean 建立连接之后，它的所有状态都应当可被保存，下一次被装载进构造工具或在运行时，就应当是上一次修改完的信息。为了能做到这一点，要把 bean 的某些字段的信息保存下来，在定义 bean 时要使它实现 java.io.Serializable 接口。例如：

public class Button implements java.io.Serializable{}

实现了序列化接口的 bean 中字段的信息将被自动保存。若不想保存某些字段的信息，则可在这些字段前冠以 transient 或 static 关键字，transient 和 static 变量的信息是不能被保存的。

通常，bean 所有公开出来的属性都应当是被保存的，也可有选择地保存内部状态。bean 开发者在修改软件时，可以添加字段，移走对其他类的引用，改变一个字段的 private/protected/public 状态，这些都不影响类的存储结构关系。

然而，当从类中删除一个字段，改变一个变量在类体系中的位置，把某个字段改成 transient/static，或原来是 transient/static，现改为别的特性时，都将引起存储关系的变化。

#### 提示：

JavaBean 组件被设计出来后，一般是以扩展名为 jar 的 Zip 格式文件存储，在 jar 中每含与 JavaBean 有关的信息，并以 MANIFEST 文件指定其中的哪些类是 JavaBean。以 jar 文件存储的 JavaBean 在网络中传送时极大地减少了数据的传输数量，并把 JavaBean 运行时所需要的一些资源捆绑在一起。随着世界各大 ISV 对 JavaBean 越来越多的支持，规范在一些细节上还在不断演化，但基本框架不会再有大的变动。