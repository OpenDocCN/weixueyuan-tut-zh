# 9.2 静态查找表—静态查找表结构

静态查找表是数据元素的线性表，可以是基于数组的顺序存储或以线性链表存储。

/* 顺序存储结构*/

typedef struct{

ElemType *elem； /* 数组基址*/

int length； /* 表长度*/

}S_TBL；

/* 链式存储结构结点类型*/

typedef struct NODE{

ElemType elem； /* 结点的值域*/

struct NODE *next； /* 下一个结点指针域*/

}NodeType；