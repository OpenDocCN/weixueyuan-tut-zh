# C++ string 字符串比较方法详解

字符串可以和类型相同的字符串相比较，也可以和具有同样字符类型的数组比较。

Basic_string 类模板既提供了  >、<、==、>=、<=、!= 等比较运算符，还提供了 compare() 函数，其中 compare() 函数支持多参数处理，支持用索引值和长度定位子串进行比较。该函数返回一个整数来表示比较结果。如果相比较的两个子串相同，compare() 函数返回 0，否则返回非零值。

## compare()函数

类 basic_string 的成员函数 compare() 的原型如下：

int compare (const basic_string& s) const;
int compare (const Ch* p) const;
int compare (size_type pos, size_type n, const basic_string& s) const;
int compare (size_type pos, size_type n, const basic_string& s,size_type pos2, size_type n2) const;
int compare (size_type pos, size_type n, const Ch* p, size_type = npos) const;

如果在使用 compare() 函数时，参数中出现了位置和大小，比较时只能用指定的子串。例如：

s.compare {pos,n, s2);

若参与比较的两个串值相同，则函数返回 0；若字符串 S 按字典顺序要先于 S2，则返回负值；反之，则返回正值。下面举例说明如何使用 string 类的 compare() 函数。

【例 1】

```
#include <iostream>
#include <string>
using namespace std;
int main ()
{
    string A ("aBcdef");
    string B ("AbcdEf");
    string C ("123456");
    string D ("123dfg");
    //下面是各种比较方法
    int m=A.compare (B); //完整的 A 和 B 的比较
    int n=A.compare(1,5,B,4,2); //"Bcdef"和"AbcdEf"比较
    int p=A.compare(1,5,B,4,2); //"Bcdef"和"Ef"比较
    int q=C.compare(0,3,D,0,3); //"123"和"123"比较
    cout << "m = " << m << ", n = " << n <<", p = " << p << ", q = " << q << endl;
    cin.get();
    return 0;
}
```

程序的执行结果为：

m = 1, n = -1, p = -1, q = 0

由此可知，string 类的比较 compare() 函数使用非常方便，而且能区分字母的大小写。建议读者多使用此函数。

## 比较运算符

String 类的常见运算符包括 >、<、==、>=、<=、!=。其意义分别为"大于"、"小于"、"等于"、"大于等于"、"小于等于"、"不等于"。

比较运算符使用起来非常方便，此处不再介绍其函数原型，读者直接使用即可。下面以例 2 进行说明。

【例 2】

```
#include <iostream>
#include <string>
using namespace std;
void TrueOrFalse (int x)
{
    cout << (x?"True":"False")<<endl;
}

int main ()
{
    string S1 = "DEF";
    string CP1 = "ABC";
    string CP2 = "DEF";
    string CP3 = "DEFG";
    string CP4 ="def";
    cout << "S1= " << S1 << endl;
    cout << "CP1 = " << CP1 <<endl;
    cout << "CP2 = " << CP2 <<endl;
    cout << "CP3 = " << CP3 <<endl;
    cout << "CP4 = " << CP4 <<endl;
    cout << "S1 <= CP1 returned ";
    TrueOrFalse (S1 <=CP1);
    cout << "S1 <= CP2 returned ";
    TrueOrFalse (S1 <= CP2);
    cout << "S1 <= CP3 returned ";
    TrueOrFalse (S1 <= CP3);
    cout << "CP1 <= S1 returned ";
    TrueOrFalse (CP1 <= S1);
    cout << "CP2 <= S1 returned ";
    TrueOrFalse (CP2 <= S1);
    cout << "CP4 <= S1 returned ";
    TrueOrFalse (CP4 <= S1);
    cin.get();
    return 0;
}
```

程序运行结果为：

S1= DEF
CP1 = ABC
CP2 = DEF
CP3 = DEFG
CP4 = def
S1 <= CP1 returned False
S1 <= CP2 returned True
S1 <= CP3 returned True
CP1 <= S1 returned True
CP2 <= S1 returned True
CP4 <= S1 returned False

由上述内容可知，使用比较运算符可以非常容易地实现字符串的大小比较。在使用时比较运算符时，读者应注意，对于参加比较的两个字符串，任一个字符串均不能为 `NULL`，否则程序会异常退出。