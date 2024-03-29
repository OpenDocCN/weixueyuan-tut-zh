# EOS 的共识机制与区块生成

在《EOSIO 技术白皮书》中，对 EOS 的共识机制 BFT-DPOS（拜占庭容错算法+权益委托共识机制）进行了说明。其中拜占庭容错算法（Byzantin eFault Tolerance）的用途是：

所有的出块者都要对所有区块签名，以确保在同一时间戳或者同一区块高度上，没有区块生产者能够同时在两个区块上签名。一旦一个区块有了 15 个区块生产者的签名，该区块就被认为是不可逆的。任一区块生产者如果想在同一时间戳或者同一区块高度的两个区块上签名，就会留下密码学证据。在这一模式下，一秒之内就可以达成不可逆的共识。

在白皮书中，EOS 对其区块的出块机制进行了介绍，转摘如下：

根据这一算法，在使用 EOSIO 软件构建的区块链上持有通证的人，可以通过一个持续进行的投票系统来选择区块生产者。任何人都可以选择参加区块生产，只要能够说服通证持有人为其投票，就会有机会参与区块的生产。

EOSIO 软件可以让区块每 0.5 秒生成一个。在任何时刻，只有一个生产者被授权生产区块。如果在计划的某个时间内没有成功出块，则跳过该块。如果有一个或更多的区块被跳过，则在区块链上会有 0.5 秒或者更久的空白。

使用 EOSIO 软件，区块的产生以 126 个区块（每个出块者六个区块，乘以 21 个出块者）为一个周期。在每个出块周期开始时，软件会根据通证持有人所投票数选出 21 个区块生产者。被选中的区块生产者的出块顺序要获得 15 个及以上的区块生产者的同意。

如果出块者错过了一个块，并且在最近 24 小时内没有产生任何块，则这个出块者将被剔除在考虑范围之外，直到他们通知区块链可以重新开始产生区块。这确保了网络的顺利运行，把被证明为不可靠的区块生产者排除在出块排序之外，这一方式使得错过区块的数量最小化。

资料来源：

*   中文译文摘自：[`blog.eosdata.io/index.php/2018/04/28/eos-io-whitepaper-v2/`](http://blog.eosdata.io/index.php/2018/04/28/eos-io-whitepaper-v2/)
*   英文版见：[`github.com/EOSIO/Documentation/blob/master/TechnicalWhitePaper.md`](https://github.com/EOSIO/Documentation/blob/master/TechnicalWhitePaper.md)