# 一千六百零二、液晶简单显示程序

1602 液晶手册提供了一个初始化过程，由于不检测“忙”位，所以程序比较复杂，而我们总结了一个更加简易方便的过程提供给大家，手册上描述的那个，大家仅仅作为了解就可以了，下面我把程序写出来大家看下，我们的初始化只用了 4 条语句，没有像手册介绍的那么繁琐。

```
#include <reg52.h>
#define LCD1602_DB P0
sbit LCD1602_RS = P1⁰;
sbit LCD1602_RW = P1¹;
sbit LCD1602_E = P1⁵;

void InitLcd1602();
void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);

void main(){
    unsigned char str[] = "Kingst Studio";
    InitLcd1602();
    LcdShowStr(2, 0, str);
    LcdShowStr(0, 1, "Welcome to KST51");
    while (1);
}
    /* 等待液晶准备好 */
void LcdWaitReady(){
    unsigned char sta;
    LCD1602_DB = 0xFF;
    LCD1602_RS = 0;
    LCD1602_RW = 1;
    do {
        LCD1602_E = 1;
        sta = LCD1602_DB; //读取状态字
        LCD1602_E = 0;
    }while (sta & 0x80); //bit7 等于 1 表示液晶正忙，重复检测直到其等于 0 为止
}
/* 向 LCD1602 液晶写入一字节命令，cmd-待写入命令值 */
void LcdWriteCmd(unsigned char cmd){
    LcdWaitReady();
    LCD1602_RS = 0;
    LCD1602_RW = 0;
    LCD1602_DB = cmd;
    LCD1602_E = 1;
    LCD1602_E = 0;
}
/* 向 LCD1602 液晶写入一字节数据，dat-待写入数据值 */
void LcdWriteDat(unsigned char dat){
    LcdWaitReady();
    LCD1602_RS = 1;
    LCD1602_RW = 0;
    LCD1602_DB = dat;
    LCD1602_E = 1;
    LCD1602_E = 0;
}
/* 设置显示 RAM 起始地址，亦即光标位置，(x,y)-对应屏幕上的字符坐标 */
void LcdSetCursor(unsigned char x, unsigned char y){
    unsigned char addr;
    if (y == 0){ //由输入的屏幕坐标计算显示 RAM 的地址
        addr = 0x00 + x; //第一行字符地址从 0x00 起始
    }else{
        addr = 0x40 + x; //第二行字符地址从 0x40 起始
    }
    LcdWriteCmd(addr | 0x80); //设置 RAM 地址
}
/* 在液晶上显示字符串，(x,y)-对应屏幕上的起始坐标，str-字符串指针 */
void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str){
    LcdSetCursor(x, y); //设置起始地址
    while (*str != '\0'){ //连续写入字符串数据，直到检测到结束符
        LcdWriteDat(*str++); //先取 str 指向的数据，然后 str 自加 1
    }
}
/* 初始化 1602 液晶 */
void InitLcd1602(){
    LcdWriteCmd(0x38); //16*2 显示，5*7 点阵，8 位数据接口
    LcdWriteCmd(0x0C); //显示器开，光标关闭
    LcdWriteCmd(0x06); //文字不动，地址自动+1
    LcdWriteCmd(0x01); //清屏
}
```

程序中有详细的注释，结合本节前面的讲解，大家自己分析下，掌握 1602 液晶的基本操作函数。LcdWriteDat(*str++)这行语句中对指针 str 的操作大家一定要理解透彻，先把 str 指向的数据取出来用，然后 str 再加 1 以指向下一个数据，这是非常常用的一种简写方式。另外关于本程序还有几点值得提一下：

*   首先，我们把程序所有的功能都使用函数模块化了，这样非常有利于程序的维护，不管要写一个什么样的功能，只要调用相应的函数就可以了，大家注意学习这种编程方法。
*   其次，我们使用液晶的习惯，也是用数学上的(x,y)坐标来进行屏幕定位，但与数学坐标系不同的是，液晶的左上角的坐标是 x=0，y=0，往右边是 x+偏移，下边是 y+偏移。
*   第三，第一次接触多个参数传递的函数，而且还带了指针类型的参数，所以多留心熟悉一下。
*   第四，读写数据和指令程序，每次都必须进行“忙”判断。
*   第五，领略一下指针在这个地方的巧妙用法，你可以尝试不用指针改写程序试试，感受一下指针的优势。