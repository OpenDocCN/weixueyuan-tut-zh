# 单片机 I2C 和 EEPROM 的综合编程

电视频道记忆功能，交通灯倒计时时间的设定，户外 LED 广告的记忆功能，都有可能用到 EEPROM 这类存储器件。这类器件的优势是存储的数据不仅可以改变，而且掉电后数据保存不丢失，因此大量应用在各种电子产品上。

我们这节课的例程，有点类似广告屏。上电后，1602 的第一行显示 EEPROM 从 0x20 地址开始的 16 个字符，第二行显示 EERPOM 从 0x40 开始的 16 个字符。我们可以通过 UART 串口通信来改变 EEPROM 内部的这个数据，并且同时也改变了 1602 显示的内容，下次上电的时候，直接会显示我们更新过的内容。

这个程序所有的相关内容，前面都已经讲过了。但是这个程序体现在了一个综合应用能力上。这个程序用到了 1602 液晶、UART 串口通信、EEPROM 读写操作等多个功能的综合应用。写个点亮小灯好简单，但是我们想真正学好单片机，必须得学会这种综合程序的应用，实现多个模块同时参与工作。因此同学们，要认认真真的把工程建立起来，一行一行的把程序编写起来，最终巩固下来。

/*****************************I2C.c 文件程序源代码*******************************/

（此处省略，可参考之前章节的代码）

/***************************Lcd1602.c 文件程序源代码*****************************/

（此处省略，可参考之前章节的代码）

/****************************eeprom.c 文件程序源代码*****************************/

（此处省略，可参考之前章节的代码）

/*****************************Uart.c 文件程序源代码*****************************/

（此处省略，可参考之前章节的代码）

```
/*****************************main.c 文件程序源代码******************************/
#include <reg52.h>
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节

void InitShowStr();
void ConfigTimer0(unsigned int ms);
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);
extern void E2Read(unsigned char *buf, unsigned char addr, unsigned char len);
extern void E2Write(unsigned char *buf, unsigned char addr, unsigned char len);
extern void UartDriver();
extern void ConfigUART(unsigned int baud);
extern void UartRxMonitor(unsigned char ms);
extern void UartWrite(unsigned char *buf, unsigned char len);

void main(){
    EA = 1; //开总中断
    ConfigTimer0(1); //配置 T0 定时 1ms
    ConfigUART(9600); //配置波特率为 9600
    InitLcd1602(); //初始化液晶
    InitShowStr(); //初始显示内容

    while (1){
        UartDriver(); //调用串口驱动
    }
}
/* 处理液晶屏初始显示内容 */
void InitShowStr(){
    unsigned char str[17];

    str[16] = '\0';//在最后添加字符串结束符，确保字符串可以结束
    E2Read(str, 0x20, 16); //读取第一行字符串，其 E2 起始地址为 0x20
    LcdShowStr(0, 0, str); //显示到液晶屏
    E2Read(str, 0x40, 16); //读取第二行字符串，其 E2 起始地址为 0x40
    LcdShowStr(0, 1, str); //显示到液晶屏
}
/* 内存比较函数，比较两个指针所指向的内存数据是否相同，
ptr1-待比较指针 1，ptr2-待比较指针 2，len-待比较长度
返回值-两段内存数据完全相同时返回 1，不同返回 0 */
bit CmpMemory(unsigned char *ptr1, unsigned char *ptr2, unsigned char len){
    while (len--){
        if (*ptr1++ != *ptr2++){ //遇到不相等数据时即刻返回 0
            return 0;
        }
    }
    return 1; //比较完全部长度数据都相等则返回 1
}
/* 将一字符串整理成 16 字节的固定长度字符串，不足部分补空格
out-整理后的字符串输出指针，in-待整理字符串指针 */
void TrimString16(unsigned char *out, unsigned char *in){
    unsigned char i = 0;
    while (*in != '\0'){ //拷贝字符串直到输入字符串结束
        *out++ = *in++;
        i++;
        if (i >= 16){ //当拷贝长度已达到 16 字节时，强制跳出循环
            break;
        }
    }
    for ( ; i<16; i++){ //如不足 16 个字节则用空格补齐
        *out++ = ' ';
    }
    *out = '\0'; //最后添加结束符
}
/* 串口动作函数，根据接收到的命令帧执行响应的动作
buf-接收到的命令帧指针，len-命令帧长度 */
void UartAction(unsigned char *buf, unsigned char len){
    unsigned char i;
    unsigned char str[17];
    unsigned char code cmd0[] = "showstr1 "; //第一行字符显示命令
    unsigned char code cmd1[] = "showstr2 "; //第二行字符显示命令

    unsigned char code cmdLen[] = { //命令长度汇总表
        sizeof(cmd0)-1, sizeof(cmd1)-1,
    };
    unsigned char code *cmdPtr[] = { //命令指针汇总表
        &cmd0[0], &cmd1[0],
    };
    for (i=0; i<sizeof(cmdLen); i++){ //遍历命令列表，查找相同命令
        if (len >= cmdLen[i]){ //首先接收到的数据长度要不小于命令长度
            if (CmpMemory(buf, cmdPtr[i], cmdLen[i])){ //比较相同时退出循环
                break;
            }
        }
    }
    switch (i){ //根据比较结果执行相应命令
        case 0:
            buf[len] = '\0'; //为接收到的字符串添加结束符
            TrimString16(str, buf+cmdLen[0]); //整理成 16 字节固定长度字符串
            LcdShowStr(0, 0, str); //显示字符串 1
            E2Write(str, 0x20, sizeof(str)); //保存字符串 1，起始地址为 0x20
            break;
        case 1:
            buf[len] = '\0'; //为接收到的字符串添加结束符
            TrimString16(str, buf+cmdLen[1]); //整理成 16 字节固定长度字符串
            LcdShowStr(0, 1, str); //显示字符串 1
            E2Write(str, 0x40, sizeof(str)); //保存字符串 2，起始地址为 0x40
            break;
        default: //未找到相符命令时，给上机发送“错误命令”的提示
            UartWrite("bad command.\r\n", sizeof("bad command.\r\n")-1);
            return;
    }
    buf[len++] = '\r'; //有效命令被执行后，在原命令帧之后添加
    buf[len++] = '\n'; //回车换行符后返回给上位机，表示已执行
    UartWrite(buf, len);
}
/* 配置并启动 T0，ms-T0 定时时间 */
void ConfigTimer0(unsigned int ms){
    unsigned long tmp; //临时变量
    tmp = 11059200 / 12; //定时器计数频率
    tmp = (tmp * ms) / 1000; //计算所需的计数值
    tmp = 65536 - tmp; //计算定时器重载值
    tmp = tmp + 33; //补偿中断响应延时造成的误差

    T0RH = (unsigned char)(tmp>>8); //定时器重载值拆分为高低字节
    T0RL = (unsigned char)tmp;
    TMOD &= 0xF0; //清零 T0 的控制位
    TMOD |= 0x01; //配置 T0 为模式 1
    TH0 = T0RH; //加载 T0 重载值
    TL0 = T0RL;
    ET0 = 1; //使能 T0 中断
    TR0 = 1; //启动 T0
}
/* T0 中断服务函数，执行串口接收监控和蜂鸣器驱动 */
void InterruptTimer0() interrupt 1{
    TH0 = T0RH; //重新加载重载值
    TL0 = T0RL;
    UartRxMonitor(1); //串口接收监控
}
```

我们在学习 UART 通信的时候，刚开始也是用的 IO 口去模拟 UART 通信过程，最终实现和电脑的通信，而后因为 STC89C52 内部具备 UART 硬件通信模块，所以我们直接可以通过配置寄存器就可以很轻松的实现单片机的 UART 通信。同样的道理，这个 I²C 通信，如果单片机内部有硬件模块的话，单片机可以直接自动实现 I²C 通信了，就不需要我们再进行 IO 口模拟起始、模拟发送、模拟结束，配置好寄存器，单片机就会把这些工作全部做了。

不过我们的 STC89C52 单片机内部不具备 I²C 的硬件模块，所以我们使用 STC89C52 进行 I²C 通信的话必须用 IO 口来模拟。使用 IO 口模拟 I²C 实际上更有利于我们彻底理解透彻 I²C 通信的实质。当然了，通过学习 IO 口模拟通信，今后如果遇到内部带 I²C 模块的单片机，也应该很轻松的搞定，使用内部的硬件模块，可以提高程序的执行效率。