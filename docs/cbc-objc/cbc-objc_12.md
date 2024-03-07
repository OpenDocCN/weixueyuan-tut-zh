# @property 的几种常用修饰词

在编程过程中，可能需要对某个属性做一些特殊的设置，例如：将某个属性设置为可读，那么就可以防止工程中对这个属性进行更改。

常用的修饰词有：nonatomic、copy、strong、assign、weak、readwrite、readonly 等。（这几个是经常使用的）。

如果@property 后不对属性进行设置，该属性同样有它的默认的一些设置。下面向大家介绍一下几种属性的用法以及默认的情况：

**nonatomic****和 atomic**

nonatomic：原名：非原子性，它涉及到多线程的相关知识。在这里我们只需要知道有 nonatomic 声明的属性，任何人任何地点任何时间都可以调用它（就是很多人可以同时使用它），所以它的缺点就是不安全，而优点就是效率高，访问速度快。

atomic(默认)：原子性。和 nonatomic 正好相反，他对属性的访问进行了限制，保护了声明属性的安全，缺点就是：访问效率低，速度慢。

**copy****、strong****和 weak**

copy：一般使用于字符串，它的特点如同它的中文翻译，将内容另外拷贝一份，保存在一个单独的存储空间中。

strong：叫做：强引用。

weak：弱引用

为了区分 strong 和 weak 以及 copy 之间的关系，我们来举一个生活中的例子：我们应该都放过风筝(如果不知道什么是风筝，你的童年太悲惨了)，在放风筝的时候我们手中必须有一根线牵引着风筝。

strong 就代表你手中的线(一支风筝上可能有很多根线，也就是说可能有多个 strong 声明的属性用的同一块内存空间)，只要还有一个人牵着风筝，风筝就不回飞走，用 OC 的话来说就是，只要还有一个用 strong 声明的属性不被销毁，纳闷这块内存中的数据就不回丢失，该内存空间就不回释放。

而对于 weak 来说，就相当于旁边看放风筝的人，他们只能用手指着你手里的风筝，大喊着“风筝风筝”，他们只能和你一起欣赏风筝，而对于是不是继续放风筝的决定权在你，如果你不在放风筝了，那他们也只能“各回各家，各找各妈”。

用 OC 的语言来表达，就是说用 weak 声明的属性虽然也可以访问内存，但是要在这块内存没有释放之前，一旦这块内存被释放之后，weak 声明的属性将失去作用。

对于 copy 来说，是最幸福的一件事了，你放着风筝被我看见了，我也想放，而且我也不差钱，我就买了一支一模一样的风筝和你一起放。你收起风筝不放了，我还可以放，copy 就是这么任性。

从 OC 的角度解释 copy，就是说带有 copy 声明的属性当指向一块内存空间时，它会完整的拷贝一份，然后用他自己的那一份，和被拷贝的内存空间没关系。

**assign****、readonly****和 readwrite**

assign 我认为是其中最简单的，它应用于 OC 的基础数据类型（NSInteger、BOOL）和 C 语言中的基本数据类型(int,float,double,char)

readwrite（默认）：它代表这个属性即可以读，也可以写(可以调用它的 get 和 set 方法)

readonly：和 readwrite 不同，它代表这个属性只能读，不能进行写操作(只能调用 get 方法，不能调用 set 方法)

**setter** **和 getter**

getter=method:在声明的这个属性可读的前提下，使用它给这个属性设定 get 方法

setter=method:同 get 方法类似，在声明的属性可写的前提下，使用这个设置可以设定这个属性的 set 方法。

（这两个设置很少用，因为只是给 get 和 set 方法换个名字而已）

在@property 的属性中，以上几个属性最常见，实际开发过程中基本上都能囊括。

对于以上的几个属性，如果你觉的自己虽然知道它的功能是什么，但是具体使用过程中总是很矛盾到底用哪个，下面帮大家总结一下：

对于基本数据类型，例如 int，double，float，char，还有 OC 语言中的 BOOL 以及 NSInteger 来说，都用 assign。

例如：@property (nonatomic,assign)int age;

对于 OC 中特有的字符串类型(NSString)来说，最常用的是 copy。

例如：@property (nonatomic,copy)NSString * name;

对于 OC 中大量的类对象，一般用 strong。

例如：@property (nonatomic,strong)Person * person;

在后面我们会提到协议，在涉及到协议中的 delegate 时，我们会用 weak。

 对于前面提到的默认属性，可以不写，但是如果不是默认属性，就要写明你的意图，建议都写上。

对于原子性和非原子性，如果不涉及到多线程的情况下，为了运行效率的提高，一般写上 nonatomic。

在默认情况下，由于每个属性都是可读可写的，所以我们可以自已自定义属性的 set 和 get 方法。

**@property****使用修饰词的建议：**

在某些特殊情况下，如果该属性已经设置为可读了，那就不要再用 getter 这个属性，因为声明了也用不了，而且 Xcode 会给你提示冲突，setter 也是一样。

在编程过程中，每个属性都有它默认的设置，但是为了程序的健壮，可读性更强，建议大家在声明属性的时候要把对属性的修饰写出来。