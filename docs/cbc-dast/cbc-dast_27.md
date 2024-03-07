# 4.3 串的堆存储结构—基于堆结构的基本运算

堆结构上的串运算仍然基于字符序列的复制进行，基本思想是：当需要产生一个新串时，要判断堆空间中是否还有存储空间，若有，则从 free 指针开始划出相应大小的区域为该串的存储区，然后根据运算求出串值，最后建立该串存储映象索引信息，并修改 free 指针。

设堆空间为： char store[SMAX+1];

自由区指针：int free;

串的存储映象类型如下：

typedef struct

{ int length; /*串长*/

int stradr; /*起始地址*/

} HString;

### 1\. 串常量赋值

void StrAssign(HString *s1,char *s2)

/*将一个字符型数组 s2 中的字符串送入堆 store 中,free 是自由区的指针*/

{ int i=0,len;

len=StrLength(s2);

if (len<0||free+len-1＞SMAX)

return 0;

else {for (i=0;i<len;i++)

store[frre+i] =s2[i];

s1.stradr=free;

s1.len.=len;

free=free+len;

}

}

算法 4.7

### 2\. 赋值一个串

void StrCopy(Hstring *s1,Hstring s2)

/*该运算将堆 store 中的一个串 s2 复制到一个新串 s1 中*/

{ int i;

if (free+s2.lengt-1>SMAX) return error ;

else { for(i=0; i<s2.length;i++)

store[free+i]=store[s2.atradr+i];

s1->length=s2.length;

s1->stradr=free;

free=free+s2.length;

}

}

算法 4.8

### 3\. 求子串

void StrSub(Hstring *t, Hstring s,int i,int len)

/*该运算将串 s 中第 i 个字符开始的长度为 len 的子串送到一个新串 t 中*/

{ int i;

if (i<0 || len<0 || len>s.len-i+1) return error ;

else { t->length=len;

t->stradr=s.stradr+i-1;

}

}

算法 4.9

### 3\. 串联接

void Concat(s1,s2,s)

HString s1,s2;

HString *s;

{ HString t;

StrCopy (s,s1);

StrCopy (&t,s2);

s->length=s1.length+s2.length;

}

算法 4.10

以上堆空间和算法是由算法编写者自己设计和编写来实现的，在这里，重点介这种存储的处理思想，很多问题及细节尚未涉及，比如，废弃串的回归、自由区的管理问题等等。在常用的高级语言及开发环境中，大多系统本身都提供了串的类型及大量的库函数，用户可直接使用，这样会使算法的设计和调试更方便容易，可靠性更高。