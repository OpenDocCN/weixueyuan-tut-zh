# 多个.c 文件的初步认识

我们上一节的这个液晶滚屏移动程序，大概有 160 行左右。随着我们硬件模块使用的增多，程序量的增大，我们往往要把程序写到多个文件里，方便代码的编写、维护和移植。

比如这个液晶滚屏程序，我们就可以把 1602 底层的功能函数专门写到一个.c 文件内，如 LcdWaitReady、LcdWriteCmd、LcdWriteDat、LcdShowStr、LcdSetCursor、InitLcd1602 这些函数，都是属于液晶底层驱动的程序代码，我们要使用液晶功能的时候，只有两个函数对我们实际功能实现部分有用，一个是 InitLcd1602 这个函数，因为需要先初始化液晶，另外一个就是 LcdShowStr 这个函数，我们只需要把要显示的内容通过参数传递给这个函数，这个函数就可以实现我们想要的显示效果，所以我们把这几个底层的液晶驱动程序都放到另外一个文件 Lcd1602.c 文件中，而我们想实现的一些比如滚动实现、中断等上层功能程序全部都放到 main.c 中，但是 main.c 文件如何调用 Lcd1602.c 文件中的函数呢？

C 语言中，有一个 extern 关键字，它有两个基本作用。

1、当一个变量的声明不在文件的开头，在它声明之前的函数想要引用的话，则应该用 extern 进行“外部变量”声明。用一个简单的程序给大家介绍一下，知道这么回事，能看懂别人写的就行，自己写就别这么用了。

```
#include <reg52.h>
sbit LED = P0⁰;
void main(){
    extern unsigned int i;
    while(1){
        LED = 0; //点亮小灯
        for(i=0;i<30000;i++); //延时
        LED = 1; //熄灭小灯
        for(i=0;i<30000;i++); //延时
    }
}
unsigned int i = 0;
// ... ...
```

变量的作用域，是从声明这个变量开始往后所有的程序，如果我们调用在前，声明在后，那么就是这么用。但是实际开发过程中，我们一般都不会这样做，所以仅仅是表达一下 extern 的这个用法，但它并不实用。

2、在一个工程中，我们为了方便管理和维护代码，用了多个.c 源文件，如果其中一个 main.c 文件要调用 Lcd1602.c 文件里的变量或者函数的时候，我们就必须得在 main.c 里边进行一下外部声明，告诉编译器这个变量或者函数是在其它文件中定义的，可以直接在这个文件中进行调用。

多.c 文件的编程方式，大家不要想象的太复杂。首先新建一个工程，一个工程代表一个完整的单片机程序，只能生成一个 hex，但是一个工程可以有很多个.c 源文件组成共同参与编译。工程建立好之后，新建文件并且保存取名为 main.c 文件，再新建一个文件并且保存取名为 Lcd1602.c 文件，下面我们就可以在两个不同文件中分别编写代码了。当然，在编写程序的过程中，不是说我们要先把 main.c 的文件全部写完，再进行 1602.c 程序的编写，而往往是交互的。比如我们先写 Lcd1602.c 文件中部分 Lcd1602 液晶的底层函数 LcdWaitReady、LcdWriteCmd、LcdWriteDat、InitLcd1602，然后编写 main.c 文件中的功能程序，在编写 main.c 文件中程序时，又有对 Lcd1602.c 底层程序的综合调用，这个时候需要 Lcd1602.c 文件提供一个被调用的函数比如 LcdShowStr，我们就可以再到 Lcd1602.c 中把这个函数完成。当然了，这仅仅是一个说明例子而已，顺序完全是没有一个标准的，实际应用中如果对程序逻辑需求了解透彻，根据自己的理解去写程序即可。那我们把 1602 整屏移动的程序改造成为多文件的程序，大家先初步认识一下。

```
/***************************Lcd1602.c 文件程序源代码*****************************/
#include <reg52.h>
#define LCD1602_DB P0
sbit LCD1602_RS = P1⁰;
sbit LCD1602_RW = P1¹;
sbit LCD1602_E = P1⁵;

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
    //bit7 等于 1 表示液晶正忙，重复检测直到其等于 0 为止
    }while (sta & 0x80);
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
/* 在液晶上显示字符串，(x,y)-对应屏幕上的起始坐标，
    str-字符串指针，len-需显示的字符长度 */
void LcdShowStr(unsigned char x, unsigned char y,
        unsigned char *str, unsigned char len){
    LcdSetCursor(x, y); //设置起始地址
    while (len--){ //连续写入 len 个字符数据
        LcdWriteDat(*str++);
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

```
/*****************************main.c 文件程序源代码******************************/
#include <reg52.h>
bit flag500ms = 0; //500ms 定时标志
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节
//待显示的第一行字符串
unsigned char code str1[] = "Kingst Studio";
//待显示的第二行字符串，需保持与第一行字符串等长，较短的行可用空格补齐
unsigned char code str2[] = "Let's move...";

void ConfigTimer0(unsigned int ms);
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y,
unsigned char *str, unsigned char len);

void main(){
    unsigned char i;
    unsigned char index = 0; //移动索引
    unsigned char pdata bufMove1[16+sizeof(str1)+16]; //移动显示缓冲区 1
    unsigned char pdata bufMove2[16+sizeof(str2)+16]; //移动显示缓冲区 2
    EA = 1; //开总中断
    ConfigTimer0(10); //配置 T0 定时 10ms
    InitLcd1602(); //初始化液晶

    //缓冲区开头一段填充为空格
    for (i=0; i<16; i++){
        bufMove1[i] = ' ';
        bufMove2[i] = ' ';
    }
    //待显示字符串拷贝到缓冲区中间位置
    for (i=0; i<(sizeof(str1)-1); i++){
        bufMove1[16+i] = str1[i];
        bufMove2[16+i] = str2[i];
    }
    //缓冲区结尾一段也填充为空格
    for (i=(16+sizeof(str1)-1); i<sizeof(bufMove1); i++){
        bufMove1[i] = ' ';
        bufMove2[i] = ' ';
    }

    while (1){
        if (flag500ms){ //每 500ms 移动一次屏幕
            flag500ms = 0;
            //从缓冲区抽出需显示的一段字符显示到液晶上
            LcdShowStr(0, 0, bufMove1+index, 16);
            LcdShowStr(0, 1, bufMove2+index, 16);
            //移动索引递增，实现左移
            index++;
            if (index >= (16+sizeof(str1)-1)){ //起始位置达到字符串尾部后即返回从头开始
                index = 0;
            }
        }
    }
}
/* 配置并启动 T0，ms-T0 定时时间 */
void ConfigTimer0(unsigned int ms){
    unsigned long tmp; //临时变量
    tmp = 11059200 / 12; //定时器计数频率
    tmp = (tmp * ms) / 1000; //计算所需的计数值
    tmp = 65536 - tmp; //计算定时器重载值
    tmp = tmp + 12; //补偿中断响应延时造成的误差
    T0RH = (unsigned char)(tmp>>8); //定时器重载值拆分为高低字节
    T0RL = (unsigned char)tmp;

    TMOD &= 0xF0; //清零 T0 的控制位
    TMOD |= 0x01; //配置 T0 为模式 1
    TH0 = T0RH; //加载 T0 重载值
    TL0 = T0RL;
    ET0 = 1; //使能 T0 中断
    TR0 = 1; //启动 T0
}
/* T0 中断服务函数，定时 500ms */
void InterruptTimer0() interrupt 1{
    static unsigned char tmr500ms = 0;
    TH0 = T0RH; //重新加载重载值
    TL0 = T0RL;
    tmr500ms++;
    if (tmr500ms >= 50){
        tmr500ms = 0;
        flag500ms = 1;
    }
}
```

我们在 main.c 中要调用 Lcd1602.c 文件中的 InitLcd1602()和 LcdShowStr 这两个函数，只需要在 main.c 中进行 extern 声明即可。大家用 Keil 软件编程试试，真正的感觉一下多.c 源文件的好处。如果这个程序给你的感觉还不深刻，那下面我们来做一个稍微大点的程序来体会一下。