# JavaBean 的属性（Simple、Indexed、Bound、Constrained）

JavaBean 的属性与 Java 程序中所指的属性，或者说与所有面向对象的程序设计语言中对象的属性是一个概念，在程序中的具体体现就是类中的变量。在 JavaBean 设计中，按照不同的作用可以将属性分为四类：Simple、Indexed、Bound、Constrained 属性。

## Simple 属性

一个简单属性表示一个伴随有一对 get/set 方法（C 语言中的过程或函数，在 Java 程序中称为“方法”）的变量。属性名与和该属性相关的 get/set 方法名对应。例如：如果有 setX 和 getX 方法，则暗指有一个名为 X 的属性；如果有一个方法名为 isX，则通常暗指 X 是一个布尔属性（即 X 的值为 true 或 false）。

【例 1】使用 Simple 属性的代码如下。

Canvas 类的源程序如下：

```
package example4-3;
import java.awt.Color;
public class Canvas
{
    public void setBackground(Color df){}
    public void setForeground(Color df){}
}
```

alden 类的源程序如下：

```
package example4-3-1;
import java.awt.Color;
public class alden extends Canvas
{
    String ourString="Hello";    //属性名为 ourString，类型为字符串
    public alden()
    {
        //alden1()是 alden1 的构造函数，与 C++中构造函数的意义相同
        setBackground(Color.red);
        setForeground(Color.blue);
    }
    //"set"属性
    public void setString(String newString)
    {
        ourString=newString;
    }
    //"get"属性
    public String getString()
    {
        return ourString;
    }
} 
```

## Indexed 属性

一个 Indexed 属性表示一个数组值。使用与该属性对应的 set/get 方法可以取得数组中的数值。该属性也可一次设置或取得整个数组的值。

【例 2】使用 Indexed 属性的代码如下：

```
package example4-4;
import java.awt.Color;
public class alden2 extends Canvas
{
    int[] dataSet={1,2,3,4,5,6};    //dataSet 是一个 indexed 属性
    public alden2()
    {
        setBackground(Color.red);
        setForeground(Color.blue);
    }
    //设置整个数组
    public void setDataSet(int[] x)
    {
        dataSet=x;
    }
    //设置数组中的单个元素值
    public void setDataSet(int index,int x)
    {
        dataSet[index]=x;
    }
    //取得整个数组值
    public int[] getDataSet()
    {
        return dataSet;
    }
    //取得数组中的指定元素值
    public int getDataSet(int x)
    {
        return dataSet[x];
    }
} 
```

## Bound 属性

Bound 属性是指当该种属性的值发生变化时，要通知其他的对象。每次属性值改变时，这种属性就引发一个 PropertyCliange 事件（在 Java 程序中，事件也是一个对象）。事件中封装了属性名、属性的原值、属性变化后的新值。这种事件传递到其他的 bean，至于接收事件的 bean 应做什么动作由其自己定义。

当 PushButton 的 background 属性与 Dialog 的 background 属性绑定时，若 PushButton 的 background 属性发生变化，则 Dialog 的 background 属性也发生同样的变化。

【例 3】使用 Bound 属性的代码如下：

```
public class alden3 extends Canvas
{
    String ourString="Hello";
    //ourString 是一个 bound 属性
    private PropertyChangeSupport changes=new PropertyChangeSupport(this);
    /*Java 是纯面向对象的语言，如果要使用某种方法则必须指明是要使用哪个对象的方法，在下面的程序中要进行点火事件的操作，这种操作所使用的方法是在 PropertyChangeSupport 类中的。所以上面声明并实例化了一个 changes 对象，在下面将使用 changes 的 firePropertyChange 方法来点火 ourString 的属性改变事件。*/
    public void setString(string newString)
    {
        String oldString=ourString;
        ourString=newString;
        //ourString 的属性值已发生变化，于是接着点火属性改变事件
        changes.firePropertyChange("ourString",oldString,newString);
    }
    public String getString()
    {
        return ourString;
    }
    /*以下代码是为开发工具所使用的。 我们不能预知 alden3 将与哪些其它的 Beans 组合成为一个应用，无法预知若 alden3 的 ourString 属性发生变化时有哪些其它的组件与此变化有关，因而 alden3 这个 Beans 要预留出一些接口给开发工具，把其它的 JavaBeans 对象与 alden3 挂接。*/
    public void addPropertyChangeListener(PropertyChangeLisener l)
    {
        changes.addPropertyChangeListener(l);
    }
    public void removePropertyChangeListener(PropertyChangeListener l)
    {
        changes.removePropertyChangeListener(l);
    }
```

通过上面的代码，开发工具调用 changes 的 addPropertyChangeListener 方法，把其他 JavaBean 注册入 ourString 属性的监听者队列 1 中，1 是一个 Vector 数组，可存储任何 Java 对象。

开发工具也可使用 changes 的 removePropertyChangeListener 方法，从 1 中注销指定的对象，使 alden3 的 ourString 属性的改变不再与这个对象有关。当然，当程序员手写代码编制程序时，也可直接调用这两个方法，把其他 Java 对象与 alden3 挂接。

## Constrained 属性

JavaBean 的 Constrained 属性，是指当这个属性的值要发生变化时，与这个属性已建立了某种连接的其他 Java 对象可否决属性值的改变。constrained 属性的监听者通过拋出 PropertyVetoException 来阻止该属性值的改变。

【例 5】下面程序中的 Constrained 属性是 PricelnCents。

```
public class JellyBeans extends Canvas
{
    private PropertyChangeSupport changes=new PropertyChangeSupport(this);
    private VetoableChangeSupport Vetos=new VetoableChangeSupport(this);
    //与前述 changes 相同，可使用 VetoableChangeSupport 对象的实例 Vetos 中的方法，在特定条件下来阻止 PriceInCents 值的改变。
    public void setPriceInCents(int newPriceInCents) throws PropertyVetoException
    {
        //方法名中 throws PropertyVetoException 的作用是当有其它 Java 对象否决 PriceInCents 的改变时，要抛出例外。
        //先保存原来的属性值
        int oldPriceInCents=ourPriceInCents;
        //点火属性改变否决事件
        vetos.fireVetoableChange("priceInCents",new Integer(OldPriceInCents),new Integer(newPriceInCents));
        /*若有其它对象否决 priceInCents 的改变，则程序抛出例外，不再继续执行下面的两条语句，方法结束。若无其它对象否决 priceInCents 的改变，则在下面的代码中把 ourPriceIncents 赋予新值，并点火属性改变事件*/
        ourPriceInCents=newPriceInCents;
        changes.firePropertyChange("priceInCents",new Integer(oldPriceInCents),new Integer(newPriceInCents));
    }
    /*与前述 changes 相同，也要为 PriceInCents 属性预留接口，使其它对象可注册入 PriceInCents 否决改变监听者队列中，或把该对象从中注销*/
    public void addVetoableChangeListener(VetoableChangeListener l)
    {
        vetos.addVetoableChangeListener(l);
    }
    public void removeVetoableChangeListener(VetoableChangeListener l)
    {
        vetos.removeVetoableChangeListener(l);
    }
}
```

从上面的例子可以看到，一个 Constrained 属性有两种监听者：属性变化监听者和否决属性改变的监听者。否决属性改变的监听者在自己的对象代码中有相应的控制语句，在监听到有 Constrained 属性要发生变化时，在控制语句中判断是否应否决这个属性值的改变。

总之，某个 bean 的 Constrained 属性值可否改变取决于其他的 bean 或者 Java 对象是否允许这种改变。允许与否的条件由其他的 bean 或 Java 对象在自己的类中进行定义。