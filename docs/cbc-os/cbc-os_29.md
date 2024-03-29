# 死锁的处理策略

为使系统不发生死锁，必须设法破坏产生死锁的四个必要条件之一，或者允许死锁产生， 但当死锁发生时能检测出死锁，并有能力实现恢复。

#### 预防死锁

设置某些限制条件，破坏产生死锁的四个必要条件中的一个或几个，以防止发生死锁。

#### 避免死锁

在资源的动态分配过程中，用某种方法防止系统进入不安全状态，从而避免死锁。

#### 死锁的检测及解除

无需釆取任何限制性措施，允许进程在运行过程中发生死锁。通过系统的检测机构及时 地检测出死锁的发生，然后釆取某种措施解除死锁。

预防死锁和避免死锁都属于事先预防策略，但预防死锁的限制条件比较严格，实现起来 较为简单，但往往导致系统的效率低，资源利用率低；避免死锁的限制条件相对宽松，资源 分配后需要通过算法来判断是否进入不安全状态，实现起来较为复杂。

死锁的几种处理策略的比较见表 2-14。

表 2-14 死锁处理策略的比较

| -- | 资源分配策略 | 各种可能模式 | 主要优点 | 主要缺点 |
| 死锁预防 | 保守，宁可资源闲置 | 一次请求所有资源，资 源剥夺，资源按序分配 | 适用于做突发式处理 的进程，不必进行剥夺 | 效率低，进程初始化时 间延长；剥夺次数过多； 不便灵活申请新资源 |
| 死锁避免 | 是”预防“和”检测“ 的折中（在运行时判断是 否可能死锁） | 寻找可能的安全允许 顺序 | 不必进行剥夺 | 必须知道将来的资源 需求；进程不能被长时间 阻塞 |
| 死锁检测 | 宽松，只要允许就分配 资源 | 定期检查死锁是否已 经发生 | 不延长进程初始化时 间，允许对死锁进行现场 处理 | 通过剥夺解除死锁，造 成损失 |