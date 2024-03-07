# C#集合简介

> 原文：[`c.biancheng.net/view/2886.html`](http://c.biancheng.net/view/2886.html)

集合与数组比较类似，都用于存放一组值，但集合中提供了特定的方法能直接操作集合中的数据，并提供了不同的集合类来实现特定的功能。

集合简单的说就是数组的升级版。他可以动态的对集合的长度（也就是集合内最大元素的个数）进行定义和维护！

所有集合类或与集合相关的接口命名空间都是 System.Collection，在该命名空间中提供的常用接口如下表所示。

| 接口名称 | 作用 |
| IEnumerable | 用于迭代集合中的项，该接口是一种声明式的接口 |
| IEnumerator | 用于迭代集合中的项，该接口是一种实现式的接口 |
| ICollection | .NET 提供的标准集合接口，所有的集合类都会直接或间接地实现这个接口 |
| IList | 继承自 IEnumerable 和 ICollection 接口，用于提供集合的项列表，并允许访问、查找集合中的项 |
| IDictionary | 继承自 IEnumerable 和 ICollection 接口，与 IList 接口提供的功能类似，但集 合中的项是以键值对的形式存取的 |
| IDictionaryEnumerator | 用于迭代 IDictionary 接口类型的集合 |

针对上表中的接口有一些常用的接口实现类，如下表所示。

| 类名称 | 实现接口 | 特点 |
| ArrayList | ICollection、IList、IEnumerable、ICloneable | 集合中元素的个数是可变的，提供添加、删除等方法 |
| Queue | ICollection、IEnumerable、ICloneable | 集合实现了先进先出的机制，即元素将在集合的尾部添加、在集合的头部移除 |
| Stack | ICollection、IEnumerable、ICloneable | 集合实现了先进后出的机制，即元素将在集合的尾部添加、在集合的尾部移除 |
| Hashtable | IDictionary、ICollection、IEnumerable、 ICloneable 等接口 | 集合中的元素是以键值对的形式存放的，是 DictionaryEntry 类型的 |
| SortedList | IDictionary、ICollection、IEnumerable、  ICloneable 等接口 | 与 Hashtable 集合类似，集合中的元素以键值对的形式存放，不同的是该集合会按照 key 值自动对集合中的元素排序 |