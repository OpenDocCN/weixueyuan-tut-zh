# Java 接口和抽象类的区别

从前面对面向对象的设计原则的讲解，读者可以了解到，其实所有的设计原则和设计模式都离不开抽象，因为只有抽象才能实现上述设计原则和设计模式。

在 Java 中，针对抽象有两种实现方式：一种是接口，一种是抽象类。很多读者对这两种实现方式比较困惑，到底是使用接口，还是使用抽象类呢？对于它们的选择甚至反映出对问题领域本质的理解，对设计意图的理解是否正确、合理？

在面向对象的设计思想中，所有的对象都是通过类来描绘的，但是反过来，并不是所有的类都是用来描绘对象的，如果一个类中没有描绘一个具体的对象，那么这样的类就是抽象类，抽象类是对那些看上去不同，但是本质上相同的具体概念的抽象，正是因为抽象的概念在问题领域没有对应的具体概念，所以抽象类是不能够实例化的。

## 基本语法区别

在 Java 中，接口和抽象类的定义语法是不一样的。这里以动物类为例来说明，其中定义接口的示意代码如下：

```
public interface Animal
{
    //所有动物都会吃
    public void eat();

    //所有动物都会飞
    public void fly();
}
```

定义抽象类的示意代码如下：

```
public abstract class Animal
{
    //所有动物都会吃
    public abstract void eat();

    //所有动物都会飞
    public void fly(){};
}
```

可以看到，在接口内只能是功能的定义，而抽象类中则可以包括功能的定义和功能的实现。在接口中，所有的属性肯定是 public、static 和 final，所有的方法都是 abstract，所以可以默认不写上述标识符；在抽象类中，既可以包含抽象的定义，也可以包含具体的实现方法。

在具体的实现类上，接口和抽象类的实 现类定义方式也是不一样的，其中接口实现类的示意代码如下：

```
public class concreteAnimal implements Animal
{
    //所有动物都会吃
    public void eat(){}

    //所有动物都会飞
    public void fly(){}
}
```

抽象类的实现类示意代码如下：

```
public class concreteAnimal extends Animal
{
    //所有动物都会吃
    public void eat(){}

    //所有动物都会飞
    public void fly(){}
}
```

可以看到，在接口的实现类中使用 implements 关键字；而在抽象类的实现类中，则使用 extends 关键字。一个接口的实现类可以实现多个接口，而一个抽象类的实现类则只能实现一个抽象类。

## 设计思想区别

从前面抽象类的具体实现类的实现方式可以看出，其实在 Java 中，抽象类和具体实现类之间是一种继承关系，也就是说如果釆用抽象类的方式，则父类和子类在概念上应该是相同的。接口却不一样，如果采用接口的方式，则父类和子类在概念上不要求相同。接口只是抽取相互之间没有关系的类的共同特征，而不用关注类之间的关系，它可以使没有层次关系的类具有相同的行为。因此，可以这样说：抽象类是对一组具有相同属性和方法的逻辑上有关系的事物的一种抽象，而接口则是对一组具有相同属性和方法的逻辑上不相关的事物的一种抽象。

仍然以前面动物类的设计为例来说明接口和抽象类关于设计思想的区别，该动物类默认所有的动物都具有吃的功能，其中定义接口的示意代码如下：

```
public interface Animal
{
    //所有动物都会吃
    public void eat();
}
```

定义抽象类的示意代码如下：

```
public abstract class Animal
{
    //所有动物都会吃
    public abstract void eat();
}
```

不管是实现接口，还是继承抽象类的具体动物，都具有吃的功能，具体的动物类的示意代码如下。

接口实现类的示意代码如下：

```
public class concreteAnimal implements Animal
{
    //所有动物都会吃
    public void eat(){}
}
```

抽象类的实现类示意代码如下：

```
public class concreteAnimal extends Animal
{
    //所有动物都会吃
    public void eat(){}
}
```

当然，具体的动物类不光具有吃的功能，比如有些动物还会飞，而有些动物却会游泳，那么该如何设计这个抽象的动物类呢？可以别在接口和抽象类中增加飞的功能，其中定义接口的示意代码如下：

```
public interface Animal
{
    //所有动物都会吃
    public void eat();

    //所有动物都会飞
    public void fly();
}
```

定义抽象类的示意代码如下：
public abstract class Animal
{
    //所有动物都会吃
    public abstract void eat();

    //所有动物都会飞
    public void fly(){};
}

这样一来，不管是接口还是抽象类的实现类，都具有飞的功能，这显然不能满足要求，因为只有一部分动物会飞，而会飞的却不一定是动物，比如飞机也会飞。那该如何设计呢？有很多种方案，比如再设计一个动物的接口类，该接口具有飞的功能，示意代码如下：

```
public interface AnimaiFly
{
    //所有动物都会飞
    public void fly();
}
```

那些具体的动物类，如果有飞的功能的话，除了实现吃的接口外，再实现飞的接口，示意代码如下：

```
public class concreteAnimal implements Animal,AnimaiFly
{
    //所有动物都会吃
    public void eat(){}

    //动物会飞
    public void fly();
}
```

那些不需要飞的功能的具体动物类只实现具体吃的功能的接口即可。另外一种解决方案是再设计一个动物的抽象类，该抽象类具有飞的功能，示意代码如下：

```
public abstract class AnimaiFly
{
    //动物会飞
    public void fly();
}
```

但此时没有办法实现那些既有吃的功能，又有飞的功能的具体动物类。因为在 Java 中具体的实现类只能实现一个抽象类。一个折中的解决办法是，让这个具有飞的功能的抽象类，继承具有吃的功能的抽象类，示意代码如下：

```
public abstract class AnimaiFly extends Animal
{
    //动物会飞
    public void fly();
}
```

此时，对那些只需要吃的功能的具体动物类来说，继承 Animal 抽象类即可。对那些既有吃的功能又有飞的功能的具体动物类来说，则需要继承 AnimalFly 抽象类。

但此时对客户端有一个问题，那就是不能针对所有的动物类都使用 Animal 抽象类来进行编程，因为 Animal 抽象类不具有飞的功能，这不符合面向对象的设计原则，因此这种解决方案其实是行不通的。

还有另外一种解决方案，即具有吃的功能的抽象动物类用抽象类来实现，而具有飞的功能的类用接口实现；或者具有吃的功能的抽象动物类用接口来实现，而具有飞的功能的类用抽象类实现。

具有吃的功能的抽象动物类用抽象类来实现，示意代码如下：

```
public abstract class Animal
{
    //所有动物都会吃
    public abstract void eat();
}
```

具有飞的功能的类用接口实现，示意代码如下：

```
public interface AnimaiFly
{
    //动物会飞
    public void fly();
}
```

既具有吃的功能又具有飞的功能的具体的动物类，则继承 Animal 动物抽象类，实现 AnimalFly 接口，示意代码如下：

```
public class concreteAnimal extends Animal implements AnimaiFly
{
    //所有动物都会吃
    public void eat(){}

    //动物会飞
    public void fly();
}
```

或者具有吃的功能的抽象动物类用接口来实现，示意代码如下：

```
public interface Animal
{
    //所有动物都会吃
    public abstract void eat();
}
```

具有飞的功能的类用抽象类实现，示意代码如下：

```
public abstract class AnimaiFly
{
    //动物会飞
    public void fly(){};
}
```

既具有吃的功能又具有飞的功能的具体的动物类，则实现 Animal 动物类接口，继承 AnimaiFly 抽象类，示意代码如下：

```
public class concreteAnimal extends AnimaiFly implements Animal
{
    //所有动物都会吃
    public void eat(){}

    //动物会飞
    public void fly();
}
```

这些解决方案有什么不同呢？再回过头来看接口和抽象类的区别：抽象类是对一组具有相同属性和方法的逻辑上有关系的事物的一种抽象，而接口则是对一组具有相同属性和方法的逻辑上不相关的事物的一种抽象，因此抽象类表示的是“is a”关系，接口表示的是“like a”关系。

假设现在要研究的系统只是动物系统，如果设计人员认为对既具有吃的功能又具有飞的功能的具体的动物类来说，它和只具有吃的功能的动物一样，都是动物，是一组逻辑上有关系的事物，因此这里应该使用抽象类来抽象具有吃的功能的动物类，即继承 Animal 动物抽象类，实现 AnimalFly 接口。

如果设计人员认为对既具有吃的功能，又具有飞的功能的具体的动物类来说，它和只具有飞的功能的动物一样，都是动物，是一组逻辑上有关系的事物，因此这里应该使用抽象类来抽象具有飞的功能的动物类，即实现 Animal 动物类接口，继承 AnimaiFly 抽象类。

假设现在要研究的系统不只是动物系统，如果设计人员认为不管是吃的功能，还是飞的功能和动物类没有什么关系，因为飞机也会飞，人也会吃，则这里应该实现两个接口来分别抽象吃的功能和飞的功能，即除实现吃的 Animal 接口外，再实现飞的 AnimalFly 接口。

从上面的分析可以看出，对于接口和抽象类的选择，反映出设计人员看待问题的不同角度，即抽象类用于一组相关的事物，表示的是“isa”的关系，而接口用于一组不相关的事物，表示的是“like a”的关系。