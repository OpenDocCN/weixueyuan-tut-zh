# 10.5 二路归并排序

二路归并排序的基本操作是将两个有序表合并为一个有序表。

设 r[u…t]由两个有序子表 r[u…v-1]和 r[v…t]组成，两个子表长度分别为 v-u、t-v+1。

合并方法为：

1.  i=u；j=v；k=u； //置两个子表的起始下标及辅助数组的起始下标
2.  若 i>v 或 j>t，转⑷ //其中一个子表已合并完，比较选取结束
3.  //选取 r[i]和 r[j]关键码较小的存入辅助数组 rf

    如果 r[i].key<r[j].key，rf[k]=r[i]； i++； k++； 转⑵

    否则，rf[k]=r[j]； j++； k++； 转⑵

4.  //将尚未处理完的子表中元素存入 rf

    如果 i<v，将 r[i…v-1]存入 rf[k…t] //前一子表非空

    如果 j<=t，将 r[i…v]存入 rf[k…t] //后一子表非空

5.  合并结束。

【算法 10.11】

void Merge(ElemType *r，ElemType *rf，int u，int v，int t)

{

for(i=u，j=v，k=u；i<v&&j<=t；k++)

{ if(r[i].key<r[j].key)

{ rf[k]=r[i]；i++；}

else

{ rf[k]=r[j]；j++；}

}

if(i<v) rf[k…t]=r[i…v-1]；

if(j<=t) rf[k…t]=r[j…t]；

}

### 一.两路归并的迭代算法

1 个元素的表总是有序的。所以对 n 个元素的待排序列，每个元素可看成 1 个有序子表。对子表两两合并生成 n/2 个子表，所得子表除最后一个子表长度可能为 1 外，其余子表长度均为 2。再进行两两合并，直到生成 n 个元素按关键码有序的表。

【算法 10.12】

void MergeSort(S_TBL *p，ElemType *rf)

{ /*对*p 表归并排序，*rf 为与*p 表等长的辅助数组*/

ElemType *q1，*q2；

q1=rf；q2=p->elem；

for(len=1；len<p->length；len=2*len) /*从 q2 归并到 q1*/

{ for(i=1；i+2*len-1<=p->length；i=i+2*len)

Merge(q2，q1，i，i+len，i+2*len-1)； /*对等长的两个子表合并*/

if(i+len-1<p->length)

Merge(q2，q1，i，i+len，p->length)； /*对不等长的两个子表合并*/

else if(i<=p->length)

while(i<=p->length) /*若还剩下一个子表，则直接传入*/

q1[i]=q2[i]；

q1<-->q2； /*交换，以保证下一趟归并时，仍从 q2 归并到 q1*/

if(q1!=p->elem) /*若最终结果不在*p 表中，则传入之*/

for(i=1；i<=p->length；i++)

p->elem[i]=q1[i]；

}

}

### 二.两路归并的递归算法

【算法 10.13】

void MSort(ElemType *p，ElemType *p1，int s，int t)

{ /*将 p[s…t]归并排序为 p1[s…t]*/

if(s==t) p1[s]=p[s]

else

{ m=(s+t)/2； /*平分*p 表*/

MSort(p，p2，s，m)； /*递归地将 p[s…m]归并为有序的 p2[s…m]*/

MSort(p，p2，m+1，t)； /*递归地将 p[m+1…t]归并为有序的 p2[m+1…t]*/

Merge(p2，p1，s，m+1，t)； /*将 p2[s…m]和 p2[m+1…t]归并到 p1[s…t]*/

}

}

void MergeSort(S_TBL *p)

{ /*对顺序表*p 作归并排序*/

MSort(p->elem，p->elem，1，p->length)；

}

【效率分析】

需要一个与表等长的辅助元素数组空间，所以空间复杂度为 O(n)。

对 n 个元素的表，将这 n 个元素看作叶结点，若将两两归并生成的子表看作它们的父结点，则归并过程对应由叶向根生成一棵二叉树的过程。所以归并趟数约等于二叉树的高度-1，即 log2n，每趟归并需移动记录 n 次，故时间复杂度为 O(nlog2n)。