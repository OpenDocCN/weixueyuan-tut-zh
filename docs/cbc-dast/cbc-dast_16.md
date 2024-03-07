# 2.4 顺序表和链表的比较

在本章介绍了线性表的逻辑结构及它的两种存储结构：顺序表和链表。

通过对它们的讨论可知它们各有优缺点，顺序存储有三个优点：

(1) 方法简单，各种高级语言中都有数组，容易实现。

(2) 不用为表示结点间的逻辑关系而增加额外的存储开销。

(3) 顺序表具有按元素序号随机访问的特点。

但它也有两个缺点：

(1) 在顺序表中做插入删除操作时，平均移动大约表中一半的元素，因此对 n 较大的顺序表效率低。

(2) 需要预先分配足够大的存储空间，估计过大，可能会导致顺序表后部大量闲置；预先分配过小，又会造成溢出。

链表的优缺点恰好与顺序表相反。在实际中怎样选取存储结构呢？通常有以下几点考虑：

**１．基于存储的考虑**

顺序表的存储空间是静态分配的，在程序执行之前必须明确规定它的存储规模，也就是说事先对＂MAXSIZE＂要有合适的设定，过大造成浪费，过小造成溢出。可见对线性表的长度或存储规模难以估计时，不宜采用顺序表；链表不用事先估计存储规模，但链表的存储密度较低，存储密度是指一个结点中数据元素所占的存储单元和整个结点所占的存储单元之比。显然链式存储结构的存储密度是小于１的。

**２．基于运算的考虑**

在顺序表中按序号访问 ai 的时间性能时 O(1)，而链表中按序号访问的时间性能 O(n)，所以如果经常做的运算是按序号访问数据元素，显然顺序表优于链表；而在顺序表中做插入、删除时平均移动表中一半的元素，当数据元素的信息量较大且表较长时，这一点是不应忽视的；在链表中作插入、删除，虽然也要找插入位置，但操作主要是比较操作，从这个角度考虑显然后者优于前者。

**３．基于环境的考虑**

顺序表容易实现，任何高级语言中都有数组类型，链表的操作是基于指针的，相对来讲前者简单些，也是用户考虑的一个因素。

总之，两中存储结构各有长短，选择那一种由实际问题中的主要因素决定。通常“较稳定”的线性表选择顺序存储，而频繁做插入删除的即动态性较强的线性表宜选择链式存储。