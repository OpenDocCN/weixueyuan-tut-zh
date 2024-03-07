# 5.4 广义表—广义表基本操作的实现

我们以头尾表示法存储广义表，讨论广义表的有关操作的实现。由于广义表的定义是递归的，因此相应的算法一般也都是递归的。

## ⒈广义表的取头、取尾

GList Head(GList ls)

{

if ls->tag = = 1

then p = ls->hp;

return p;

}

算法 5.6

GList Tail(GList ls)

{

if ls->tag = = 1

then p = ls->tp;

return p;

}

算法 5.7

## ⒉建立广义表的存储结构

int Create(GList *ls, char * S)

{ Glist p; char *sub;

if StrEmpty(S) *ls = NULL;

else {

if (!(*ls = (GList)malloc(sizeof(GLNode)))) return 0;

if (StrLength(S) = = 1) {

(*ls)->tag = 0;

(*ls)->data = S;

}

else {

(*ls)->tag = 1;

p = *ls;

hsub =SubStr(S,2,StrLength(S)-2);

do {

sever(sub,hsub);

Create(&(p->ptr.hp), sub);

q = p;

if (!StrEmpty(sub)){

if (!(p = (GList)malloc(sizeof(GLNode)))) return 0;;

p->tag = 1;

q->ptr.tp = p;

}

}while (!StrEmpty(sub));

q->ptr.tp = NULL;

}

}

return 1;

}

算法 5.8

int sever(char *str, char *hstr)

{

int n = StrLength(str);

i= 1; k = 0;

for (i = 1, k = 0; i <= n || k != 0; ++i)

{

ch=SubStr(str,i,1);

if (ch = = '(') ++k;

else if (ch = = ')') --k;

}

if (i <= n)

{

hstr =SubStr(str,1,i-2);

str= SubStr(str,i,n-i+1);

}

else {

StrCopy(hstr,str);

ClearStr(str);

}

}

算法 5.9

## ⒊以表头、表尾建立广义表

int Merge(GList ls1,GList ls2, Glist *ls)

{

if (!(*ls = (GList)malloc(sizeof(GLNode)))) return 0;

*ls->tag = 1;

*ls->hp = ls1;

*ls->tp = ls2;

return 1;

}

算法 5.10

## ⒋求广义表的深度

int Depth(GList ls)

{

if (!ls)

return 1; /*空表深度为 1*/

if (ls->tag = = 0)

return 0; /*单元素深度为 0*/

for (max = 0,p = ls; p; p = p->ptr.tp) {

dep = Depth(p->ptr.hp); /*求以 p->ptr.hp 尾头指针的子表深度*/

if (dep > max) max = dep;

}

return max+1; /*非空表的深度是各元素的深度的最大值加 1*/

}

算法 5.11

## ⒌复制广义表

int CopyGList(GList ls1, GList *ls2)

{

if (!ls1) *ls2 = NULL; /*复制空表*/

else {

if (!(*ls2 = (Glist)malloc(sizeof(Glnode)))) return 0; /*建表结点*/

(*ls2)->tag = ls1->tag;

if (ls1->tag = = 0) (*ls2)->data = ls1->data; /*复制单元素*/

else {

CopyGList(&((*ls2)->ptr.hp), ls1->ptr.hp); /*复制广义表 ls1->ptr.hp 的一个副本*/

CopyGList(&((*ls2)->ptr.tp) , ls1->ptr.tp); /*复制广义表 ls1->ptr.tp 的一个副本*/

}

}

return 1;

}

算法 5.12