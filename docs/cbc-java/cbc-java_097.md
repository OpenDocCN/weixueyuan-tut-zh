# Java 认识类和对象

具有相同特性（数据元素）和行为（功能）的对象的抽象就是类，因此对象的抽象是类，类的具体化就是对象，也可以说类的实例是对象。

让我们来看看人类所具有的一些特征，这些特征包括属性（一些参数、数值）以及方法（一些行为，他能干什么）。

每个人都有身高、体重、年龄、血型等属性，人会劳动、会直立行走、会用自己的头脑去创造工具等方法。人之所以能区别于其他类型的动物，是因为每个人都具有“人”这个群体的属性与方法。

“人类”只是一个抽象的概念，它仅仅是一个概念，是不存在的实体！但是所有具备“人类”这个群体的属性与方法的对象都叫人！这个对象“人” 是实际存在的实体！每个人都是“人”这个群体的一个对象。

老虎为什么不是人？因为它不具备“人”这个群体的属性与方法，老虎不会直立行走，不会使用工具等，所以说老虎不是人！也就是说，类是概念模型，定义对象的所有特性和所需的操作，对象是真实的模型，是一个具体的实体。

由此可见，类是描述了一组有相同特性（属性）和相同行为（方法）的一组对象的集合。

对象或实体所拥有的特征在类中表示时称为类的属性。例如，每个人都具有姓名、年龄和体重，这是所有人共有的特征。但是每一个对象的属性值又各不相同，例如，小明和小红都具有体重这个属性，但是他们的体重值是不同的。

对象执行的操作称为类的方法。比如，“人”这个对象都具有的行为是“吃饭”，因此，吃饭就是“人”类的一个方法。

综上所述，类是描述实体的“模板”和“原型”，它定义了属于这个类的对象所应该具有的状态和行为。比如一名学生在上课。一名正在上课的学生是类，它定义的信息有：姓名、上课。

使用该类定义的不同姓名的人在上课是对象，他们可能是小明、小红、小丽、张会等。在 Java 面向对象编程中，用自定义的类模型可以创建该类的一个实例，也就是对象。

类是实体对象的概念模型，因此通常是笼统的、不具体的。表 1 给出了类和对象的更多示例。

表 1 类和对象的示例

| 类 | 对象 |
| 人 | 正在清洁的环卫工人小刘 |
| 教室里的学生张丽 |
| 汽车 | 一辆黄色的宝马跑车 |
| 一辆白色的林肯轿车 |
| 动物 | 一只叫“猫咪”的小花猶 |
| 一只叫“欢欢”的贵宾犬 |

类是构造面向对象程序的基本单位，是抽取了同类对象的共同属性和方法所形成的对象或实体的“模板”。而对象是现实世界中实体的描述，对象要创建才存在，有了对象才能对对象进行操作。类是对象的模板，对象是类的实例。