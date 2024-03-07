# DS1302 的 BURST 模式

进行产品开发的时候，逻辑的严谨性非常重要，如果一个产品或者程序逻辑上不严谨，就有可能出现功能上的错误。比如我们 15.3.4 节里的这个程序，我们再回顾一下，当单片机定时器时间到了 200ms 后，我们连续把 DS1302 的时间参数的 7 个字节读了出来。但是不管怎么读，都会有一个时间差，在极端的情况下就会出现这样一种情况：假如我们当前的时间是 00:00:59，我们先读秒，读到的秒是 59，然后再去读分钟，而就在读完秒到还未开始读分钟的这段时间内，刚好时间进位了，变成了 00:01:00 这个时间，我们读到的分钟就是 01，显示在液晶上就会出现一个 00:01:59，这个时间很明显是错误的。出现这个问题的概率极小，但却是实实在在可能存在的。

为了解决这个问题，芯片厂家肯定要给我们提供一种解决方案，这就是 DS1302 的突发模式。突发模式也分为 RAM 突发模式和时钟突发模式，RAM 部分我们不讲，我们只看和时钟相关的 clock burst mode。

当我们写指令到 DS1302 的时候，只要我们将要写的 5 位地址全部写 1，即读操作用 0xBF，写操作用 0xBE，这样的指令送给 DS1302 之后，它就会自动识别出来是 burst 模式，马上把所有的 8 个字节同时锁存到另外的 8 个字节的寄存器缓冲区内，这样时钟继续走，而我们读数据是从另外一个缓冲区内读取的。同样的道理，如果我们用 burst 模式写数据，那么我们也是先写到这个缓冲区内，最终 DS1302 会把这个缓冲区内的数据一次性送到它的时钟寄存器内。

要注意的是，不管是读还是写，只要使用时钟的 burst 模式，则必须一次性读写 8 个寄存器，要把时钟的寄存器完全读出来或者完全写进去。

下边就提供一个 burst 模式的例程给大家学习一下，程序的功能还是与上一节一样的。

/***************************Lcd1602.c 文件程序源代码*****************************/

（此处省略，可参考之前章节的代码）

```
/*****************************main.c 文件程序源代码******************************/
#include <reg52.h>

sbit DS1302_CE = P1⁷;
sbit DS1302_CK = P3⁵;
sbit DS1302_IO = P3⁴;

bit flag200ms = 0; //200ms 定时标志
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节

void ConfigTimer0(unsigned int ms);
void InitDS1302();
void DS1302BurstRead(unsigned char *dat);
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);

void main(){
    unsigned char psec=0xAA; //秒备份，初值 AA 确保首次读取时间后会刷新显示
    unsigned char time[8]; //当前时间数组
    unsigned char str[12]; //字符串转换缓冲区

    EA = 1; //开总中断
    ConfigTimer0(1); //T0 定时 1ms
    InitDS1302(); //初始化实时时钟
    InitLcd1602(); //初始化液晶

    while (1){
        if (flag200ms){ //每 200ms 读取依次时间
            flag200ms = 0;
            DS1302BurstRead(time); //读取 DS1302 当前时间

            if (psec != time[0]){ //检测到时间有变化时刷新显示
                str[0] = '2'; //添加年份的高 2 位：20
                str[1] = '0';
                str[2] = (time[6] >> 4) + '0'; //“年”高位数字转换为 ASCII 码
                str[3] = (time[6]&0x0F) + '0'; //“年”低位数字转换为 ASCII 码
                str[4] = '-'; //添加日期分隔符
                str[5] = (time[4] >> 4) + '0'; //“月”
                str[6] = (time[4]&0x0F) + '0';
                str[7] = '-';
                str[8] = (time[3] >> 4) + '0'; //“日”
                str[9] = (time[3]&0x0F) + '0';
                str[10] = '\0';
                LcdShowStr(0, 0, str); //显示到液晶的第一行

                str[0] = (time[5]&0x0F) + '0'; //“星期”
                str[1] = '\0';
                LcdShowStr(11, 0, "week");
                LcdShowStr(15, 0, str); //显示到液晶的第一行

                str[0] = (time[2] >> 4) + '0'; //“时”
                str[1] = (time[2]&0x0F) + '0';
                str[2] = ':'; //添加时间分隔符
                str[3] = (time[1] >> 4) + '0'; //“分”
                str[4] = (time[1]&0x0F) + '0';
                str[5] = ':';
                str[6] = (time[0] >> 4) + '0'; //“秒”
                str[7] = (time[0]&0x0F) + '0';
                str[8] = '\0';
                LcdShowStr(4, 1, str); //显示到液晶的第二行

                psec = time[0]; //用当前值更新上次秒数
            }
        }
    }
}

/* 发送一个字节到 DS1302 通信总线上 */
void DS1302ByteWrite(unsigned char dat){
    unsigned char mask;
    for (mask=0x01; mask!=0; mask<<=1){ //低位在前，逐位移出
        if ((mask&dat) != 0){ //首先输出该位数据
            DS1302_IO = 1;
        }else{
            DS1302_IO = 0;
        }
        DS1302_CK = 1; //然后拉高时钟
        DS1302_CK = 0; //再拉低时钟，完成一个位的操作
    }
    DS1302_IO = 1; //最后确保释放 IO 引脚
}
/* 由 DS1302 通信总线上读取一个字节 */
unsigned char DS1302ByteRead(){
    unsigned char mask;
    unsigned char dat = 0;

    for (mask=0x01; mask!=0; mask<<=1){ //低位在前，逐位读取
        if (DS1302_IO != 0){ //首先读取此时的 IO 引脚，并设置 dat 中的对应位
            dat |= mask;
        }
        DS1302_CK = 1; //然后拉高时钟
        DS1302_CK = 0; //再拉低时钟，完成一个位的操作
    }
    return dat; //最后返回读到的字节数据
}
/* 用单次写操作向某一寄存器写入一个字节，reg-寄存器地址，dat-待写入字节 */
void DS1302SingleWrite(unsigned char reg, unsigned char dat){
    DS1302_CE = 1; //使能片选信号
    DS1302ByteWrite((reg<<1)|0x80); //发送写寄存器指令
    DS1302ByteWrite(dat); //写入字节数据
    DS1302_CE = 0; //除能片选信号
}
/* 用单次读操作从某一寄存器读取一个字节，reg-寄存器地址，返回值-读到的字节 */
unsigned char DS1302SingleRead(unsigned char reg){
    unsigned char dat;

    DS1302_CE = 1; //使能片选信号
    DS1302ByteWrite((reg<<1)|0x81); //发送读寄存器指令
    dat = DS1302ByteRead(); //读取字节数据
    DS1302_CE = 0; //除能片选信号
    return dat;
}
/* 用突发模式连续写入 8 个寄存器数据，dat-待写入数据指针 */
void DS1302BurstWrite(unsigned char *dat){
    unsigned char i;

    DS1302_CE = 1;
    DS1302ByteWrite(0xBE); //发送突发写寄存器指令

    for (i=0; i<8; i++){ //连续写入 8 字节数据
        DS1302ByteWrite(dat[i]);
    }
    DS1302_CE = 0;
}
/* 用突发模式连续读取 8 个寄存器的数据，dat-读取数据的接收指针 */
void DS1302BurstRead(unsigned char *dat){
    unsigned char i;

    DS1302_CE = 1;
    DS1302ByteWrite(0xBF); //发送突发读寄存器指令
    for (i=0; i<8; i++){ //连续读取 8 个字节
        dat[i] = DS1302ByteRead();
    }
    DS1302_CE = 0;
}
/* DS1302 初始化，如发生掉电则重新设置初始时间 */
void InitDS1302(){
    unsigned char dat;
    unsigned char code InitTime[] = { //2013 年 10 月 8 日 星期二 12:30:00
        0x00,0x30,0x12, 0x08, 0x10, 0x02, 0x13
    };

    DS1302_CE = 0; //初始化 DS1302 通信引脚
    DS1302_CK = 0;
    dat = DS1302SingleRead(0); //读取秒寄存器

    if ((dat & 0x80) != 0){ //由秒寄存器最高位 CH 的值判断 DS1302 是否已停止
        DS1302SingleWrite(7, 0x00); //撤销写保护以允许写入数据
        DS1302BurstWrite(InitTime); //设置 DS1302 为默认的初始时间
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
/* T0 中断服务函数，执行 200ms 定时 */
void InterruptTimer0() interrupt 1{
    static unsigned char tmr200ms = 0;
    TH0 = T0RH; //重新加载重载值
    TL0 = T0RL;
    tmr200ms++;
    if (tmr200ms >= 200){ //定时 200ms
        tmr200ms = 0;
        flag200ms = 1;
    }
}
```