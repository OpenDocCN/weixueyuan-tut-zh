# 经典进程同步问题 4：吸烟者问题

## 向题描述

假设一个系统有三个抽烟者进程和一个供应者进程。每个抽烟者不停地卷烟 并抽掉它，但是要卷起并抽掉一支烟，抽烟者需要有三种材料：烟草、纸和胶水。三个抽烟 者中，第一个拥有烟草、第二个拥有纸，第三个拥有胶水。供应者进程无限地提供三种材料， 供应者每次将两种材料放到桌子上，拥有剩下那种材料的抽烟者卷一根烟并抽掉它，并给供 应者一个信号告诉完成了，供应者就会放另外两种材料在桌上，这种过程一直重复（让三个 抽烟者轮流地抽烟)。

## 问题分析

1) 关系分析。供应者与三个抽烟者分别是同步关系。由于供应者无法同时满足两个或 以上的抽烟者，三个抽烟者对抽烟这个动作互斥（或由三个抽烟者轮流抽烟得知

2) 整理思路。显然这里有四个进程。供应者作为生产者向三个抽烟者提供材料。

3) 信号量设置。信号量 offer1、offer2、offer3 分别表示烟草和纸组合的资源、烟草和 胶水组合的资源、纸和胶水组合的资源。信号量 finish 用于互斥进行抽烟动作。

代码如下：

```
int random; //存储随机数
semaphore offer1=0; //定义信号量对应烟草和纸组合的资源
semaphore offer2=0; //定义信号量对应烟草和胶水组合的资源
semaphore offer3=0; //定义信号量对应纸和胶水组合的资源
semaphore finish=0; //定义信号量表示抽烟是否完成
//供应者
while(1){
    random = 任意一个整数随机数;
    random=random% 3;
    if(random==0)
        V(offerl) ; //提供烟草和纸
    else if(random==l) 
        V(offer2);  //提供烟草和胶水
    else
        V(offer3)  //提供纸和胶水
    // 任意两种材料放在桌子上;
    P(finish);
}
//拥有烟草者
while(1){
    P (offer3);
    // 拿纸和胶水，卷成烟，抽掉;
    V(finish);
}
//拥有纸者
while(1){
    P(offer2);
    // 烟草和胶水,卷成烟，抽掉；
    V(finish);
}
//拥有胶水者
while(1){
    P(offer1);
    // 拿烟草和纸，卷成烟，抽掉;
    v(finish);
}
```