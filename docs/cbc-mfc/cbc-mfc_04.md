# MFC 中典型类和函数的介绍

在 MFC 中，典型的类有 CString、CRect、CDialog 等等。这里篇幅有限，以 CString 类的使用为例做一个详细说明。类的使用主要还是使用类的方法，可以查看类的定义，查看这个类提供了哪些方法，在使用的时候，只需要注意方法的返回值和参数即可。这些类的使用方法是通用的，大家在学习的时候要注意体会和总结。

## MFC 字符串操作类 CString

CString 是用来表示可变长度的字符串的类。在 MFC 中，这个类的使用频率非常高。CString 是 MFC 经过封装后特有的一种字符串类型，较 string 或 iftream 或 oftream 的字符串有更多明显的有点，如使用 CString 可不指明内存大小，CString 会根据需要自行分配，除此之外还可以对字符串进行拼接操作。因为 MFC 已经对这个类进行了封装，我们可以在 MFC 程序任何地方调用它提供的一系列操作字符串的方法，示例代码如下：

```
//CString 使用方法示例
CString strTemp=_T("MFC Tutorial");
//(1)获取字符串的长度
//结果： iLength=12,中间的空格也算一个字符
int iLength=strTemp.GetLength();
//(2)获取字符串 strTemp 的第三个字符
//结果：cFlag=‘C’
char cFlag=strTemp.GetAt(2);
//(3)数据类型转换 将 char 型字符转换为 CString 类型 数据类型的转换内容较多，后面会单独讨论
//结果：strS=_T("C");
CString strS=_T("");
strS.Format(_T("%c"),cFlag);
//(4)字符串比较 Compare 方法，如果两个一致，返回 0;不一致，返回-1
//结果：iResult0==0；iResult1==-1；iResult2=1
int iResult0=strTemp.Compare(_T("MFC Tutorial"));
int iResult1=strTemp.Compare(_T("MFC TutorialX"));
int iResult2=strTemp.Compare(_T("MFC TutoRiaL"));
//(5)字符串比较 CompareNoCase 方法，如果两个不区分大小写一致，返回 0;
//结果：iResult3==0；iResult4==0；iResult5==-120
int iResult3=strTemp.CompareNoCase(_T("MFC Tutorial"));
int iResult4=strTemp.CompareNoCase(_T("MFC TutoRiaL"));
int iResult5=strTemp.CompareNoCase(_T("MFC TutoRiaLX"));
//(5)改为小写
//结果 mfc tutorial
CString strLower=strTemp.MakeLower();
//(6)改为大写
//结果 MFC TUTORIAL
CString strUpper=strTemp.MakeUpper();
//(6)获取逆序的字符串
//结果 LAIROTUT CFM
CString strReverse=strTemp.MakeReverse();
```

对代码的说明：
1) CString 是 MFC 提供的类，这个例子中列举了这个类的几个使用方法。这些方法是如何封装的对我们使用者来说是透明的，我们不可暂时不必深究。暂时只需要掌握如何使用这些方法即可。使用的时候，也只需关注方法的原型，参数和返回值即可。同样，对于使用 MFC 提供的其他类封装的方法原理是一样的，希望大家仔细体会，融会贯通。

2) CString 在 MFC 中应用非常广泛，后面将会给出这个类的具体使用方法示例，可以本章的从源码库中获取。

## 典型函数

在 MFC 中，经常会看到以 Afx 开头的一些函数，如下所示：

```
CWindApp* AfxGetApp();
HINSTANCE AfxGetInstanceHandle( );
HINSTANCE AfxGetResourceHandle( );
int AfxMessageBox( LPCTSTR lpszText, UINT nType = MB_OK, UINT nIDHelp = 0 );
```

这里说明一下，Afx 前缀的函数代表应用程序框架（Application Framework）函数。应用程序框架实际上是一套辅助我们生成应用程序的框架模型。该模型把多个类进行了一个有机的集成，可以根据该模型提供的方案来设计自己的应用程序。在 MFC 中，以 Afx 为前缀的函数都是全局函数，可以在程序的任何地方调用他们。