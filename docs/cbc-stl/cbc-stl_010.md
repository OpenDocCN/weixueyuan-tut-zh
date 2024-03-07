# C++ string 字符串修改和替换方法详解

字符串内容的变化包括修改和替换两种。本节将分别讲解字符串内容的修改和字符串内容的替换。

## 字符串内容的修改

可以通过使用多个函数修改字符串的值。例如 assign()，operator=，erase()，交换（swap），插入（insert）等。另外，还可通过 append() 函数添加字符。

下面逐一介绍各成员函数的使用方法。

#### assign()函数

使用 assign() 函数可以直接给字符串赋值。该函数既可以将整个字符串赋值给新串，也可以将字符串的子串赋值给新串。其在 basic_string 中的原型为：

basic_string& assign (const E*s); //直接使用字符串赋值
basic_string& assign (const E*s, size_type n);
basic_string& assign (const basic_string & str, size_type pos, size_type n);
//将 str 的子串赋值给调用串
basic_string& assign (const basic_string& str);    //使用字符串的“引用”賦值
basic_string& assign (size_type n, E c) ; //使用 n 个重复字符賦值
basic_string& assign (const_iterator first, const_iterator last);    //使用迭代器赋值

以上几种方法在例 1 中均有所体现。请读者参考下述代码。

【例 1】

```
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string str1 ("123456");
    string str;
    str.assign (str1); //直接赋值
    cout << str << endl;
    str.assign (str1, 3, 3); //赋值给子串
    cout << str << endl;
    str.assign (str1,2,str1.npos);//赋值给从位置 2 至末尾的子串
    cout << str << endl;
    str.assign (5,'X'); //重复 5 个'X'字符
    cout << str << endl;
    string::iterator itB;
    string::iterator itE;
    itB = str1.begin ();
    itE = str1.end();
    str.assign (itB, (--itE)); //从第 1 个至倒数第 2 个元素，赋值给字符串 str
    cout << str << endl;
    return 0;
}
```

#### operator= 函数

operator= 的功能就是赋值。

#### erase()函数

erase() 函数的原型为：

iterator erase (iterator first, iterator last);
iterator erase (iterator it);
basic_string& erase (size_type p0 = 0, size_type n = npos);

erase() 函数的使用方法为：

str.erase (str* begin(), str.end());
或 str.erase (3);

#### swap()函数

swap()函数的原型为：

void swap (basic_string& str);

swap()函数的使用方法为：

string str2 ("abcdefghijklmn");
str.swap (str2);

#### insert()函数

insert () 函数的原型为：

basic_string& insert (size_type p0 , const E * s); //插人 1 个字符至字符串 s 前面
basic_string& insert (size_type p0 , const E * s, size_type n); // 将 s 的前 3 个字符插入 p0 位置
basic_string& insert (size_type p0, const basic_string& str);
basic_string& insert (size_type p0, const basic_string& str,size_type pos, size_type n); //选取 str 的子串
basic_string& insert (size_type p0, size_type n, E c); //在下标 p0 位置插入  n 个字符 c
iterator insert (iterator it, E c); //在 it 位置插入字符 c
void insert (iterator it, const_iterator first, const_iterator last); //在字符串前插入字符
void insert (iterator it, size_type n, E c) ; //在 it 位置重复插入 n 个字符 c

insert() 函数的使用方法为：

```
string A("ello");
string B ;
B.insert(1,A);
cout << B << endl;
A = "ello";
B = "H";
B.insert (1,"yanchy ",3);
cout<< B <<endl;
A = "ello";
B = "H";
B.insert (1,A,2,2);
cout << B << endl;
A="ello";
B.insert (1 , 5 , 'C');
cout << B << endl;
A = "ello";
string::iterator it = B.begin () +1;
const string:: iterator itF = A.begin();
const string:: iterator itG = A.end();
B.insert(it,itF,itG);
cout << B << endl;
```

#### append 函数

append() 函数的原型为：

basic_string& append (const E * s); //在原始字符串后面追加字符串 s
basic_string& append (const E * s, size_type n);//在原始字符串后面追加字符串 s 的前 n 个字符
basic_string& append (const basic_string& str, size_type pos,size_type n);//在原始字符串后面追加字符串 s 的子串 s [ pos,…,pos +n -1]
basic_string& append (const basic_string& str);
basic_string& append (size_type n, E c); //追加 n 个重复字符
basic_string& append (const_iterator first, const_iterator last); //使用迭代器追加

append() 函数的使用方法为：

```
A = "ello";
cout << A << endl;
cout << B << endl;
B.append(A);
cout << B << endl;
A = "ello";
cout << A << endl;
cout << B << endl;
B.append("12345",2);
cout << B << endl;
A = "ello";
cout << A << endl;
cout << B << endl;
B.append("12345",2,3);
cout << B << endl;
A = "ello";
B = "H";
cout << A << endl;
cout << B << endl;
B.append (10, 'a');
cout << B << endl;
A = "ello";
B = 'H';
cout << A << endl ;
cout << B << endl;
B.append(A.begin(), A, end());
cout << B << endl;
```

下面通过一个完整的例子介绍这些函数的使用：

```
#include <iostream>
#include <string>
using namespace std;
int main ()
{
    string str1 ("123456");
    string str2 ("abcdefghijklmn");
    string str;
    str.assign(str1);
    cout << str << endl;
    str.assign (str1 , 3, 3);
    cout << str << endl;
    str.assign (str1, 2, str1.npos);
    cout << str << endl;
    str.assign (5, 'X');
    cout << str << endl;
    string::iterator itB;
    string::iterator itE;
    itB = str1.begin ();
    itE = str1.end();
    str.assign (itB, (--itE));
    cout << str << endl;
    str = str1;
    cout << str << endl;
    str.erase(3);
    cout << str << endl;
    str.erase (str.begin (), str.end());
    cout << ":" << str << ":" << endl;
    str.swap(str2);
    cout << str << endl;
    string A ("ello");
    string B ("H");
    B.insert (1, A);
    cout << B << endl;
    A = "ello";
    B ='H';
    B.insert (1, "yanchy ", 3);
    cout << "插入: " << B << endl;
    A = "ello";
    B = "H";
    B.insert(1,A,2,2);
    cout << "插入:" << B << endl;
    A = "ello";
    B = "H";
    B.insert (1,5,'C');
    cout << "插入:" << B << endl;
    A = "ello";
    B = "H";
    string::iterator it = B.begin () +1;
    const string::iterator itF = A.begin ();
    const string::iterator itG = A.end ();
    B.insert(it,itF,itG);
    cout<<"插入："<< B << endl;
    A = "ello";
    B = "H";
    cout << "A = " << A <<", B = " << B << endl ;
    B.append (A);
    cout << "追加：" << B << endl;
    B = "H";
    cout << "A = "<< A << ", B= " << B << endl;
    B.append("12345", 2);
    cout << "追加：" << B << endl;
    A = "ello";
    B = "H";
    cout << "A = " << A << ", B= " << B << endl;
    B.append ("12345", 2, 3);
    cout << "追加：" << B << endl;
    A = "ello";
    B = "H";
    cout << "A = " << A <<", B = " << B << endl;
    B.append (10 , 'a');
    cout << "追加:"<< B << endl;
    A = "ello";
    B = "H";
    cout << "A = " << A << ", B = " << B << endl;
    B.append(A.begin() , A.end());
    cout << "追加:" << B << endl;
    cin.get();
    return 0;
}
```

程序运行结果：

123456
456
3456
XXXXX
12345
123456
123
::
abcdefghijklmn
Hello
插入: Hyan
插入:Hlo
插入:HCCCCC
插入：Hello
A = ello, B = H
追加：Hello
A = ello, B= H
追加：H12
A = ello, B= H
追加：H345
A = ello, B = H
追加:Haaaaaaaaaa
A = ello, B = H
追加:Hello

## 字符串内容的替换

如果在一个字符串中标识出具体位置，便可以通过下标操作修改指定位置字符的值，或者替换某个子串。完成此项操作需要使用 string 类的成员函数 replace()。

replace() 函数的原型如下：

basic_string& replace (size_type p0, size_type n0, const E * s); //使用字符串 s 中的 n 个字符，从源串的位置 P0 处开始替换
basic_string& replace (size_type p0, size_type n0, const E *s, size_type n); //使用字符串 s 中的 n 个字符，从源串的位置 P0 处开始替换 1 个字符
basic_string& replace (size_type p0, size_type n0, const basic_string& str); //使用字符串 s 中的 n 个字符，从源串的位置 P0 处开始替换
basic_string& replace (size_type p0, size_type n0, const basic_string& str, size_type pos, size_type n); //使用串 str 的子串 str [pos, pos + n-1] 替换源串中的内容，从位置 p0 处开始替换，替换字符 n0 个
basic_string& replace (size_type p0, size_type n0, size_type n, E c); //使用 n 个字符 'c' 替换源串中位置 p0 处开始的 n0 个字符
basic_string& replace (iterator first0, iterator last0, const E * s);//使用迭代器替换，和 1) 用法类似
basic_string& replace (iterator first0, iterator last0, const E * s, size_type n);//和 2) 类似
basic_string& replace (iterator first0, iterator last0, const basic_string& str); //和 3) 类似
basic_string& replace (iterator first0, iterator last0, size_type n, E c); //和 5) 类似
basic_string& replace (iterator first0, iterator last0, const_iterator first, const_iterator last); //使用迭代器替换

该函数的使用方法参照下面的程序：

```
#include <iostream>
#include <string>
using namespace std;
int main ()
{
    string var ("abcdefghijklmn");
    const string dest ("1234");
    string dest2 ("567891234");
    var.replace (3,3, dest);
    cout << "1: " << var << endl;
    var = "abcdefghijklmn";
    var.replace (3,1, dest.c_str(), 1, 3);
    cout << "2: " << var << endl;
    var ="abcdefghijklmn";
    var.replace (3, 1, 5, 'x');
    cout << "3: " << var << endl;
    string::iterator itA, itB;
    string::iterator itC, itD;
    itA = var.begin();
    itB = var.end();
    var = "abcdefghijklmn";
    var.replace (itA, itB, dest);
    cout << "4: " << var << endl;
    itA = var.begin ();
    itB = var.end();
    itC = dest2.begin () +1;
    itD = dest2.end ();
    var = "abodefghijklmn";
    var.replace (itA, itB, itC, itD);
    cout << "5: " << var << endl;
    var = "abcdefghijklmn";
    var.replace (3, 1, dest.c_str(), 4); //这种方式会限定字符串替换的最大长度
    cout <<"6: " << var << endl;
    return 0;
}
```

程序执行结果为：

1: abc1234ghijklmn
2: abc234efghijklmn
3: abcxxxxxefghijklmn
4: 1234
5: 67891234efghijklmn
6: abc1234efghijklmn

本节讲述了诸多可进行字符串内容的修改和替换的函数及其使用方法，并给出了例题。由于每个函数可能有多个原型，希望读者根据自己的情况，掌握其中的一种或两种，以满足自己使用的需要。同时，希望读者能够对照例题的执行效果，认真阅读本章节中的源代码，彻底掌握本节内容。