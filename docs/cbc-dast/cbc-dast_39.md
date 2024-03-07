# 6.2 基本操作与存储—二叉树的基本操作及实现

## 1．二叉树的基本操作

二叉树的基本操作通常有以下几种：

（1）Initiate（bt）建立一棵空二叉树。

（2）Create（x，lbt，rbt）生成一棵以 x 为根结点的数据域信息，以二叉树 lbt 和 rbt 为左子树和右子树的二叉树。

（3）InsertL（bt，x，parent）将数据域信息为 x 的结点插入到二叉树 bt 中作为结点 parent 的左孩子结点。如果结点 parent 原来有左孩子结点，则将结点 parent 原来的左孩子结点作为结点 x 的左孩子结点。

（4）InsertR（bt，x，parent）将数据域信息为 x 的结点插入到二叉树 bt 中作为结点 parent 的右孩子结点。如果结点 parent 原来有右孩子结点，则将结点 parent 原来的右孩子结点作为结点 x 的右孩子结点。

（5）DeleteL（bt，parent）在二叉树 bt 中删除结点 parent 的左子树。

（6）DeleteR（bt，parent）在二叉树 bt 中删除结点 parent 的右子树。

（7）Search（bt，x）在二叉树 bt 中查找数据元素 x。

（8）Traverse（bt）按某种方式遍历二叉树 bt 的全部结点。

## 2．算法的实现

算法的实现依赖于具体的存储结构，当二叉树采用不同的存储结构时，上述各种操作的实现算法是不同的。下面讨论基于二叉链表存储结构的上述操作的实现算法。

（1）Initiate（bt）初始建立二叉树 bt，并使 bt 指向头结点。在二叉树根结点前建立头结点，就如同在单链表前建立的头结点，可以方便后边的一些操作实现。

int Initiate (BiTree *bt)

{/*初始化建立二叉树*bt 的头结点*/

if((*bt=(BiTNode *)malloc(sizeof(BiTNode)))==NULL)

return 0;

*bt->lchild=NULL;

*bt->rchild=NULL;

return 1;

}

算法 6.1

（2）Create（x，lbt，rbt）建立一棵以 x 为根结点的数据域信息，以二叉树 lbt 和 rbt 为左右子树的二叉树。建立成功时返回所建二叉树结点的指针；建立失败时返回空指针。

BiTree Create（elemtype x，BiTree lbt，BiTree rbt）

{/*生成一棵以 x 为根结点的数据域值以 lbt 和 rbt 为左右子树的二叉树*/

BiTree p;

if ((p=(BiTNode *)malloc(sizeof(BiTNode)))==NULL) return NULL;

p->data=x;

p->lchild=lbt;

p->rchild=rbt;

return p;

}

算法 6.2

（3）InsertL（bt，x，parent）

BiTree InsertL（BiTree bt，elemtype x，BiTree parent）

{/*在二叉树 bt 的结点 parent 的左子树插入结点数据元素 x*/

BiTree p;

if (parent==NULL)

{ printf(“\n 插入出错”)；

return NULL;

}

if ((p=(BiTNode *)malloc(sizeof(BiTNode)))==NULL) return NULL;

p->data=x;

p->lchild=NULL;

p->rchild=NULL;

if (parent->lchild==NULL) parent->lchild=p;

else {p->lchild=parent->lchild;

parent->lchild=p;

}

return bt;

}

算法 6.3

（4）InsertR（bt，x，parent）功能类同于（3），算法略。

（5）DeleteL（bt，parent）在二叉树 bt 中删除结点 parent 的左子树。当 parent 或 parent 的左孩子结点为空时删除失败。删除成功时返回根结点指针；删除失败时返回空指针。

BiTree DeleteL（BiTree bt，BiTree parent）

{/*在二叉树 bt 中删除结点 parent 的左子树*/

BiTree p;

if (parent==NULL||parent->lchild==NULL)

{ printf(“\n 删除出错”)；

return NULL’

}

p=parent->lchild;

parent->lchild=NULL;

free(p); /*当 p 为非叶子结点时，这样删除仅释放了所删子树根结点的空间，*/

/*若要删除子树分支中的结点，需用后面介绍的遍历操作来实现。*/

return br;

}

算法 6.4

（6）DeleteR（bt，parent）功能类同于（5），只是删除结点 parent 的右子树。算法略。操作 Search（bt，x）实际是遍历操作 Traverse（bt）的特例，关于二叉树的遍历操作的实现，将在下一节中重点介绍。