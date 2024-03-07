# 单片机串口通信原理和控制程序

我们前边学串口通信的时候，比较注重的是串口底层时序上的操作过程，所以例程都是简单的收发字符或者字符串。在实际应用中，往往串口还要和电脑上的上位机软件进行交互，实现电脑软件发送不同的指令，单片机对应执行不同操作的功能，这就要求我们组织一个比较合理的通信机制和逻辑关系，用来实现我们想要的结果。

本节所提供程序的功能是，通过电脑串口调试助手下发三个不同的命令，第一条指令：buzz on 可以让蜂鸣器响；第二条指令：buzz off 可以让蜂鸣器不响；第三条指令：showstr ，这个命令空格后边，可以添加任何字符串，让后边的字符串在 1602 液晶上显示出来，同时不管发送什么命令，单片机收到后把命令原封不动的再通过串口发送给电脑，以表示“我收到了„„你可以检查下对不对”。这样的感觉是不是更像是一个小项目了呢？

对于串口通信部分来说，单片机给电脑发字符串好说，有多大的数组，我们就发送多少个字节即可，但是单片机接收数据，接收多少个才应该是一帧完整的数据呢？数据接收起始头在哪里，结束在哪里？这些我们在接收到数据前都是无从得知的。那怎么办呢？

我们的编程思路基于这样一种通常的事实：当需要发送一帧（多个字节）数据时，这些数据都是连续不断的发送的，即发送完一个字节后会紧接着发送下一个字节，期间没有间隔或间隔很短，而当这一帧数据都发送完毕后，就会间隔很长一段时间（相对于连续发送时的间隔来讲）不再发送数据，也就是通信总线上会空闲一段较长的时间。于是我们就建立这样一种程序机制：设置一个软件的总线空闲定时器，这个定时器在有数据传输时（从单片机接收角度来说就是接收到数据时）清零，而在总线空闲时（也就是没有接收到数据时）时累加，当它累加到一定时间（例程里是 30ms）后，我们就可以认定一帧完整的数据已经传输完毕了，于是告诉其它程序可以来处理数据了，本次的数据处理完后就恢复到初始状态，再准备下一次的接收。那么这个用于判定一帧结束的空闲时间取多少合适呢？它取决于多个条件，并没有一个固定值，我们这里介绍几个需要考虑的原则：第一，这个时间必须大于波特率周期，很明显我们的单片机接收中断产生是在一个字节接收完毕后，也就是一个时刻点，而其接收过程我们的程序是无从知晓的，因此在至少一个波特率周期内你绝不能认为空闲已经时间达到了。第二，要考虑发送方的系统延时，因为不是所有的发送方都能让数据严格无间隔的发送，因为软件响应、关中断、系统临界区等等操作都会引起延时，所以还得再附加几个到十几个 ms 的时间。我们选取的 30ms 是一个折中的经验值，它能适应大部分的波特率（大于 1200）和大部分的系统延时（PC 机或其它单片机系统）情况。

我先把这个程序最重要的 UART.c 文件中的程序贴出来，一点点给大家解析，这个是实际项目开发常用的用法，大家一定要认真弄明白。

```
/*****************************Uart.c 文件程序源代码*****************************/
#include <reg52.h>

bit flagFrame = 0; //帧接收完成标志，即接收到一帧新数据
bit flagTxd = 0; //单字节发送完成标志，用来替代 TXD 中断标志位
unsigned char cntRxd = 0; //接收字节计数器
unsigned char pdata bufRxd[64]; //接收字节缓冲区

extern void UartAction(unsigned char *buf, unsigned char len);

/* 串口配置函数，baud-通信波特率 */
void ConfigUART(unsigned int baud){
    SCON = 0x50; //配置串口为模式 1
    TMOD &= 0x0F; //清零 T1 的控制位
    TMOD |= 0x20; //配置 T1 为模式 2
    TH1 = 256 - (11059200/12/32)/baud; //计算 T1 重载值
    TL1 = TH1; //初值等于重载值
    ET1 = 0; //禁止 T1 中断
    ES = 1; //使能串口中断
    TR1 = 1; //启动 T1
}
/* 串口数据写入，即串口发送函数，buf-待发送数据的指针，len-指定的发送长度 */
void UartWrite(unsigned char *buf, unsigned char len){
    while (len--){ //循环发送所有字节
        flagTxd = 0; //清零发送标志
        SBUF = *buf++; //发送一个字节数据
        while (!flagTxd); //等待该字节发送完成
    }
}
/* 串口数据读取函数，buf-接收指针，len-指定的读取长度，返回值-实际读到的长度 */
unsigned char UartRead(unsigned char *buf, unsigned char len){
    unsigned char i;
    //指定读取长度大于实际接收到的数据长度时，
    //读取长度设置为实际接收到的数据长度
    if (len > cntRxd){
        len = cntRxd;
    }
    for (i=0; i<len; i++){ //拷贝接收到的数据到接收指针上
        *buf++ = bufRxd[i];
    }
    cntRxd = 0; //接收计数器清零
    return len; //返回实际读取长度
}
/* 串口接收监控，由空闲时间判定帧结束，需在定时中断中调用，ms-定时间隔 */
void UartRxMonitor(unsigned char ms){
    static unsigned char cntbkp = 0;
    static unsigned char idletmr = 0;

    if (cntRxd > 0){ //接收计数器大于零时，监控总线空闲时间
        if (cntbkp != cntRxd){ //接收计数器改变，即刚接收到数据时，清零空闲计时
            cntbkp = cntRxd;
            idletmr = 0;
        }else{ //接收计数器未改变，即总线空闲时，累积空闲时间
            if (idletmr < 30){ //空闲计时小于 30ms 时，持续累加
                idletmr += ms;
                if (idletmr >= 30){ //空闲时间达到 30ms 时，即判定为一帧接收完毕
                    flagFrame = 1; //设置帧接收完成标志
                }
            }
        }
    }else{
        cntbkp = 0;
    }
}
/* 串口驱动函数，监测数据帧的接收，调度功能函数，需在主循环中调用 */
void UartDriver(){
    unsigned char len;
    unsigned char pdata buf[40];

    if (flagFrame){ //有命令到达时，读取处理该命令
        flagFrame = 0;
        len = UartRead(buf, sizeof(buf)); //将接收到的命令读取到缓冲区中
        UartAction(buf, len); //传递数据帧，调用动作执行函数
    }
}
/* 串口中断服务函数 */
void InterruptUART() interrupt 4{
    if (RI){ //接收到新字节
        RI = 0; //清零接收中断标志位
        //接收缓冲区尚未用完时，保存接收字节，并递增计数器
        if (cntRxd < sizeof(bufRxd)){{
            bufRxd[cntRxd++] = SBUF;
        }
    }
    if (TI){ //字节发送完毕
        TI = 0; //清零发送中断标志位
        flagTxd = 1; //设置字节发送完成标志
    }
}
```

大家可以对照注释和前面的讲解分析下这个 Uart.c 文件，在这里指出其中的两个要点希望大家多注意下。

1、接收数据的处理，在串口中断中，将接收到的字节都存入缓冲区 bufRxd 中，同时利用另外的定时器中断通过间隔调用 UartRxMonitor 来监控一帧数据是否接收完毕，判定的原则就是我们前面介绍的空闲时间，当判定一帧数据结束完毕时，设置 flagFrame 标志，主循环中可以通过调用 UartDriver 来检测该标志，并处理接收到的数据。当要处理接收到的数据时，先通过串口读取函数 UartRead 把接收缓冲区 bufRxd 中的数据读取出来，然后再对读到的数据进行判断处理。也许你会说，既然数据都已经接收到 bufRxd 中了，那我直接在这里面用不就行了嘛，何必还得再拷贝到另一个地方去呢？我们设计这种双缓冲的机制，主要是为了提高串口接收到响应效率：首先如果你在 bufRxd 中处理数据，那么这时侯就不能再接收任何数据，因为新接收的数据会破坏原来的数据，造成其不完整和混乱；其次，这个处理过程可能会耗费较长的时间，比如说上位机现在就给你发来一个延时显示的命令，那么在这个延时的过程中你都无法去接收新的命令，在上位机看来就是你暂时失去响应了。而使用这种双缓冲机制就可以大大改善这个问题，因为数据拷贝所需的时间是相当短的，而只要拷贝出去后，bufRxd 就可以马上准备去接收新数据了。

2、串口数据写入函数 UartWrite，它把数据指针 buf 指向的数据块连续的由串口发送出去。虽然我们的串口程序启用了中断，但这里的发送功能却没有在中断中完成，而是仍然靠查询发送中断标志 flagTxd（因中断函数内必须清零 TI，否则中断会重复进入执行，所以另置了一个 flagTxd 来代替 TI）来完成，当然也可以采用先把发送数据拷贝到一个缓冲区中，然后再在中断中发缓冲区数据发送出去的方式，但这样一是要耗费额外的内存，二是使程序更复杂。这里也还是想告诉大家，简单方式可以解决的问题就不要搞得更复杂。

```
/*****************************main.c 文件程序源代码******************************/
#include <reg52.h>
sbit BUZZ = P1⁶; //蜂鸣器控制引脚
bit flagBuzzOn = 0; //蜂鸣器启动标志
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节

void ConfigTimer0(unsigned int ms);
extern void UartDriver();
extern void ConfigUART(unsigned int baud);
extern void UartRxMonitor(unsigned char ms);
extern void UartWrite(unsigned char *buf, unsigned char len);
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);
extern void LcdAreaClear(unsigned char x, unsigned char y, unsigned char len);

void main(){
    EA = 1; //开总中断
    ConfigTimer0(1); //配置 T0 定时 1ms
    ConfigUART(9600); //配置波特率为 9600
    InitLcd1602(); //初始化液晶

    while (1){
        UartDriver(); //调用串口驱动
    }
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
/* 串口动作函数，根据接收到的命令帧执行响应的动作
buf-接收到的命令帧指针，len-命令帧长度 */
void UartAction(unsigned char *buf, unsigned char len){
    unsigned char i;
    unsigned char code cmd0[] = "buzz on"; //开蜂鸣器命令
    unsigned char code cmd1[] = "buzz off"; //关蜂鸣器命令
    unsigned char code cmd2[] = "showstr "; //字符串显示命令
    unsigned char code cmdLen[] = { //命令长度汇总表
        sizeof(cmd0)-1, sizeof(cmd1)-1, sizeof(cmd2)-1,
    };

    unsigned char code *cmdPtr[] = { //命令指针汇总表
        &cmd0[0], &cmd1[0], &cmd2[0],
    };

    for (i=0; i<sizeof(cmdLen); i++){ //遍历命令列表，查找相同命令
        if (len >= cmdLen[i]){ //首先接收到的数据长度要不小于命令长度
            if (CmpMemory(buf, cmdPtr[i], cmdLen[i])){ //比较相同时退出循环
                break;
            }
        }
    }
    switch (i){ //循环退出时 i 的值即是当前命令的索引值
        case 0:
            flagBuzzOn = 1; //开启蜂鸣器
            break;
        case 1:
            flagBuzzOn = 0; //关闭蜂鸣器
            break;
        case 2:
            buf[len] = '\0'; //为接收到的字符串添加结束符
            LcdShowStr(0, 0, buf+cmdLen[2]); //显示命令后的字符串
            i = len - cmdLen[2]; //计算有效字符个数
            if (i < 16){ //有效字符少于 16 时，清除液晶上的后续字符位
                LcdAreaClear(i, 0, 16-i);
            }
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
    if (flagBuzzOn){ //执行蜂鸣器鸣叫或关闭
        BUZZ = ~BUZZ;
    }else{
        BUZZ = 1;
    }
    UartRxMonitor(1); //串口接收监控
}
```

main 函数和主循环的结构我们已经做过很多了，就不多说了，这里重点把串口接收数据的具体解析方法给大家分析一下，这种用法具有很强的普遍性，掌握并灵活运用它可以使你将来的开发工作事半功倍。

首先来看 CmpMemory 函数，这个函数很简单，就是比较两段内存数据，通常都是数组中的数据，函数接收两段数据的指针，然后逐个字节比较——if (*ptr1++ != *ptr2++)，这行代码既完成了两个指针指向的数据的比较，又在比较完后把两个指针都各自+1，从这里是不是也能领略到一点 C 语言的简洁高效的魅力呢。这个函数的用处自然就是用来比较我们接收到的数据和事先放在程序里的命令字符串是否相同，从而找出相符的命令了。

接下来是 UartAction 函数对接收数据的解析和处理方法，先把接收的数据与所支持的命令字符串逐条比较，这个比较中首先要确保接收的长度大于命令字符串的长度，然后再用上述的 CmpMemory 函数逐字节比较，如果比较相同就立即退出循环，不同则继续对比下一条命令。当找到相符的命令字符串时，最终 i 的值就是该命令在其列表中的索引位置，当遍历完命令列表都没有找到相符的命令时，最终 i 的值将等于命令总数，那么接下来就用 switch 语句根据 i 的值来执行具体的动作，这个就不需要再详细说明了。

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
    } while (sta & 0x80); //bit7 等于 1 表示液晶正忙，重复检测直到其等于 0 为止
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
        LcdWriteDat(*str++);
    }
}
/* 区域清除，清除从(x,y)坐标起始的 len 个字符位 */
void LcdAreaClear(unsigned char x, unsigned char y, unsigned char len){
    LcdSetCursor(x, y); //设置起始地址
    while (len--){ //连续写入空格
        LcdWriteDat(' ');
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

液晶文件与上一个例程的液晶文件基本是一样的，唯一的区别是删掉了一个本例中用不到的全屏清屏函数，其实留着这个函数也没关系，只是 Keil 会提示一个警告，告诉你有未被调用的函数而已，可以不理会它。

经过这几个多文件工程的练习后，大家是否发现，在采用多文件模块化编程后，不光是某些函数，甚至整个 c 文件，如有需要，我们都可以直接复制到其它的新工程中使用，非常方便功能程序的移植，这样随着实践积累的增加，你会发现工作效率变得越来越高了。