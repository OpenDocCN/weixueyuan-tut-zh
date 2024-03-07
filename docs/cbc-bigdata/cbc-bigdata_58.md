# 数据挖掘之关联规则分析简介

> 原文：[`c.biancheng.net/view/3703.html`](http://c.biancheng.net/view/3703.html)

关联分析是指从大量数据中发现项集之间有趣的关联和相关联系。关联分析的一个典型例子是购物篮分析。在大数据时代，关联分析是最常见的数据挖掘任务之一。

## 概述

关联分析是一种简单、实用的分析技术，是指发现存在于大量数据集中的关联性或相关性，从而描述一个事物中某些属性同时岀现的规律和模式。

关联分析可从大量数据中发现事物、特征或者数据之间的，频繁出现的相互依赖关系和关联关系。这些关联并不总是事先知道的，而是通过数据集中数据的关联分析获得的。

关联分析对商业决策具有重要的价值，常用于实体商店或电商的跨品类推荐，购物车联合营销，货架布局陈列，联合促销，市场营销等，来达到关联项互相销量提升，改善用户体验，减少上货员与用户投入时间，寻找高潜用户的目的。

通过对数据集进行关联分析可得出形如“由于某些事件的发生而引起另外一些事件的发生”之类的规则。

例如，“67% 的顾客在购买啤酒的同时也会购买尿布”，因此通过合理的啤酒和尿布的货架摆放或捆绑销售可提高超市的服务质量和效益。“'C# 语言’课程优秀的同学，在学习’数据结构’时为优秀的可能性达 88%”，那么就可以通过强化“C# 语言”的学习来提高教学效果。

关联分析的一个典型例子是购物篮分析。通过发现顾客放入其购物篮中的不同商品之间的联系，可分析顾客的购买习惯。通过了解哪些商品频繁地被顾客同时购买，可以帮助零售商制定营销策略。其他的应用还包括价目表设计、商品促销、商品的排放和基于购买模式的顾客划分等。例如，洗发水与护发素的套装；牛奶与面包间临摆放；购买该产品的用户又买了那些其他商品等。

除了上面提到的一些商品间存在的关联现象外，在医学方面，研究人员希望能够从已有的成千上万份病历中找到患某种疾病的病人的共同特征，从寻找出更好的预防措施。另外，通过对用户银行信用卡账单的分析也可以得到用户的消费方式，这有助于对相应的商品进行市场推广。关联分析的数据挖掘方法已经涉及了人们生活的很多方面，为企业的生产和营销及人们的生活提供了极大的帮助。

## 基本概念

通过频繁项集挖掘可以发现大型事务或关系数据集中事物与事物之间有趣的关联，进而帮助商家进行决策，以及设计和分析顾客的购买习惯。例如，表 1 是一个超市的几名顾客的交易信息，其中，TID 代表交易号，Items 代表一次交易的商品。

**表 1 关联分析样本数据集**

| TID | Items |
| --- | --- |
| 001 | Cola, Egg, Ham |
| 002 | Cola, Diaper, Beer |
| 003 | Cola, Diaper, Beer, Ham |
| 004 | Diaper, Beer |

通过对这个交易数据集进行关联分析，可以找出关联规则，即｛Diaper｝→｛Beer｝。它代表的意义是，购买了 Diaper 的顾客会购买 Beer。这个关系不是必然的，但是可能性很大，这就已经足够用来辅助商家调整 Diaper 和 Beer 的摆放位置了，例如，通过摆放在相近的位置，或进行捆绑促销来提高销售量。

关联分析常用的一些基本概念如下。

| 名称 | 说明 |
| --- | --- |
| 事务 | 每一条交易数据称为一个事务，例如，表 1 包含了 4 个事务。 |
| 项 | 交易的每一个物品称为一个项，如 Diaper、Beer 等。 |
| 项集 | 包含零个或多个项的集合叫作项集，如｛Beer,Diaper｝、｛Beer,Cola,Ham｝。 |
| k-项集 | 包含 k 个项的项集叫作 k-项集，例如，｛Cola,Beer,Ham｝叫作 3-项集。 |
| 支持度计数 | 一个项集出现在几个事务当中，它的支持度计数就是几。例如,｛Diaper,Beer｝出现在事务 002、003 和 004 中，所以它的支持度计数是 3。 |
| 支持度 | 支持度计数除于总的事务数。例如，上例中总的事务数为 4，｛Diaper,Beer｝的支持度计数为 3，所以对｛Diaper,Beer｝的支持度为 75%，这说明有 75% 的人同时买了 Diaper 和 Beer。 |
| 频繁项集 | 支持度大于或等于某个阈值的项集就叫作频繁项集。例如，阈值设为 50% 时，因为｛Diaper,Beer｝的支持度是 75%，所以它是频繁项集。 |
| 前件和后件 | 对于规则｛A｝叫作前件，｛E｝叫作后件。 |
| 置信度 | 对于规则｛A}→{B｝，它的置信度为｛A,B｝的支持度计数除以｛A｝的支持度计数。例如，规则｛Diaper｝→｛Beer｝的置信度为 3/3，即 100%，这说明买了 Diaper 的人 100% 也买了 Beer。 |
| 强关联规则 | 大于或等于最小支持度阈值和最小置信度阈值的规则叫作强关联规则。通常意义上说的关联规则都是指强关联规则。关联分析的最终目标就是要找出强关联规则。 |

## 关联分析步骤

一般来说，对于一个给定的交易事务数据集，关联分析就是指通过用户指定最小支持度和最小置信度来寻求强关联规则的过程。关联分析一般分为两大步：发现频繁项集和发现关联规则。

#### 1\. 发现频繁项集

发现频繁项集是指通过用户给定的最小支持度，寻找所有频繁项集，即找出不少于用户设定的最小支持度的项目子集。

事实上，这些频繁项集可能具有包含关系。例如,项集｛Diaper,Beer,Cob｝就包含了项集｛Diaper,Beer｝。一般地，只需关心那些不被其他频繁项集所包含的所谓最大频繁项集的集合。发现所有的频繁项集是形成关联规则的基础。

由事物数据集产生的频繁项集的数量可能非常大，因此，从中找出可以推导出其他所有的频繁项集的、较小的、具有代表性的项集将是非常有用的。

| 名称 | 说明 |
| --- | --- |
| 闭项集 | 如果项集 X 是闭的，而且它的直接超集都不具有和它相同的支持度计数，则 X 是闭项集。 |
| 频繁闭项集 | 如果项集 X 是闭的，并且它的支持度大于或等于最小支持度阈值，则 X 是频繁闭项集。 |
| 最大频繁项集 | 如果项集 X 是频繁项集，并且它的直接超集都不是频繁的，则 X 为最大频繁项集。 |

最大频繁项集都是闭的，因为任何最大频繁项集都不可能与它的直接超集具有相同的支持度计数。最大频繁项集有效地提供了频繁项集的紧凑表示。换句话说，最大频繁项集形成了可以导出所有频繁项集的最小项集的集合。

#### 2\. 发现关联规则

发现关联规则是指通过用户给定的最小置信度，在每个最大频繁项集中寻找置信度不小于用户设定的最小置信度的关联规则。

相对于第一步来讲，第二步的任务相对简单，因为它只需要在已经找出的频繁项集的基础上列出所有可能的关联规则。由于所有的关联规则都是在频繁项集的基础上产生的，已经满足了支持度阈值的要求，所以第二步只需要考虑置信度阈值的要求，只有那些大于用户给定的最小置信度的规则才会被留下来。