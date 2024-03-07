# 单片机电子时钟程序设计

共用体除非必要，否则我们不推荐使用，枚举的用法比较简单，在本书 19 章的项目实践中有很好的示例，这节课我们先来练习一下结构体的使用。下边这个程序的功能是一个带日期的电子钟，相当于一个简易万年历了，并且加入了按键调时功能。学有余力的同学看到这里，不妨先不看我们提供的代码，自己写写试试。如果能够独立写一个按键可调的万年历程序，单片机可以说基本入门了。如果自己还不能够独立完成这个程序，那么还是老规矩，先抄并且理解，而后自己独立默写出来，并且要边默写边理解。

本例直接忽略了星期这项内容，通过上、下、左、右、回车、ESC 这 6 个按键可以调整时间。这也是一个具有综合练习性质的实例，虽然在功能实现上没有多少难度，但要进行的操作却比较多而且烦琐，同学们可以从中体会到把繁杂的功能实现分解为一步步函数操作的必要性以及方便灵活性。简单说一下这个程序的几个要点，方便大家阅读理解程序。

1.  把 DS1302 的底层操作封装为一个 DS1302.c 文件，对上层应用提供基本的实时时间的操作接口，这个文件也是我们的又一个功能模块了，我们的积累也越来越多了。
2.  定义一个结构体类型 sTime 用来封装日期时间的各个元素，又用该结构体定义了一个时间缓冲区变量 bufTime 来暂存从 DS1302 读出的时间和设置时间时的设定值。需要注意的是在其它文件中要使用这个结构体变量时，必须首先再声明一次 sTime 类型；
3.  定义一个变量 setIndex 来控制当前是否处于设置时间的状态，以及设置时间的哪一位，该值为 0 就表示正常运行，1～12 分别代表可以修改日期时间的 12 个位；
4.  由于这节课的程序功能要进行时间调整，用到了 1602 液晶的光标功能，添加了设置光标的函数，我们要改变哪一位的数字，就在 1602 对应位置上进行光标闪烁，所以 Lcd1602.c 在之前文件的基础上添加了两个控制光标的函数；
5.  时间的显示、增减、设置移位等上层功能函数都放在 main.c 中来实现，当按键需要这些函数时则在按键文件中做外部声明，这样做是为了避免一组功能函数分散在不同的文件内而使程序显得凌乱。

```
/***************************DS1302.c 文件程序源代码*****************************/
#include <reg52.h>

sbit DS1302_CE = P1⁷;
sbit DS1302_CK = P3⁵;
sbit DS1302_IO = P3⁴;

struct sTime { //日期时间结构体定义
    unsigned int year; //年
    unsigned char mon; //月
    unsigned char day; //日
    unsigned char hour; //时
    unsigned char min; //分
    unsigned char sec; //秒
    unsigned char week; //星期
};

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
/* 获取实时时间，即读取 DS1302 当前时间并转换为时间结构体格式 */
void GetRealTime(struct sTime *time){
    unsigned char buf[8];

    DS1302BurstRead(buf);
    time->year = buf[6] + 0x2000;
    time->mon = buf[4];
    time->day = buf[3];
    time->hour = buf[2];
    time->min = buf[1];
    time->sec = buf[0];
    time->week = buf[5];
}
/* 设定实时时间，时间结构体格式的设定时间转换为数组并写入 DS1302 */
void SetRealTime(struct sTime *time){
    unsigned char buf[8];

    buf[7] = 0;
    buf[6] = time->year;
    buf[5] = time->week;
    buf[4] = time->mon;
    buf[3] = time->day;
    buf[2] = time->hour;
    buf[1] = time->min;
    buf[0] = time->sec;
    DS1302BurstWrite(buf);
}
/* DS1302 初始化，如发生掉电则重新设置初始时间 */
void InitDS1302(){
    unsigned char dat;
    struct sTime code InitTime[] = { //2013 年 10 月 8 日 12:30:00 星期二
        0x2013,0x10,0x08, 0x12,0x30,0x00, 0x02
    };

    DS1302_CE = 0; //初始化 DS1302 通信引脚
    DS1302_CK = 0;
    dat = DS1302SingleRead(0); //读取秒寄存器

    if ((dat & 0x80) != 0){ //由秒寄存器最高位 CH 的值判断 DS1302 是否已停止
        DS1302SingleWrite(7, 0x00); //撤销写保护以允许写入数据
        SetRealTime(&InitTime); //设置 DS1302 为默认的初始时间
    }
}
```

DS1302.c 最终向外提供出与具体时钟芯片寄存器位置无关的、由时间结构类型 sTime 作为接口的实时时间的读取和设置函数，如此处理体现了我们前面提到过的层次化编程的思想。应用层可以不关心底层实现细节，底层实现的改变也不会对应用层造成影响，比如说日后你可能需要换一款时钟芯片，而它与 DS1302 的操作和时间寄存器顺序是不同的，那么你需要做的也仅是针对这款新的时钟芯片设计出底层操作函数，最终提供出同样的以 sTime 为接口的操作函数即可，应用层无需做任何的改动。

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
/* 打开光标的闪烁效果 */
void LcdOpenCursor(){
    LcdWriteCmd(0x0F);
}
/* 关闭光标显示 */
void LcdCloseCursor(){
    LcdWriteCmd(0x0C);
}
/* 初始化 1602 液晶 */
void InitLcd1602(){
    LcdWriteCmd(0x38); //16*2 显示，5*7 点阵，8 位数据接口
    LcdWriteCmd(0x0C); //显示器开，光标关闭
    LcdWriteCmd(0x06); //文字不动，地址自动+1
    LcdWriteCmd(0x01); //清屏
}
```

为了本例的具体需求，在之前文件的基础上添加两个控制光标效果打开和关闭的函数，虽然函数都很简单，但为了保持程序整体上良好的模块化和层次化，还是应该在液晶驱动文件内以函数的形式提供，而不是由应用层代码直接来调用具体的液晶写命令操作。

/***************************keyboard.c 文件程序源代码****************************/

（此处省略，可参考之前章节的代码）

```
/*****************************main.c 文件程序源代码******************************/
#include <reg52.h>

struct sTime { //日期时间结构体定义
    unsigned int year;
    unsigned char mon;
    unsigned char day;
    unsigned char hour;
    unsigned char min;
    unsigned char sec;
    unsigned char week;
};

bit flag200ms = 1; //200ms 定时标志
struct sTime bufTime; //日期时间缓冲区
unsigned char setIndex = 0; //时间设置索引
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节

void ConfigTimer0(unsigned int ms);
void RefreshTimeShow();
extern void InitDS1302();
extern void GetRealTime(struct sTime *time);
extern void SetRealTime(struct sTime *time);
extern void KeyScan();
extern void KeyDriver();
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);
extern void LcdSetCursor(unsigned char x, unsigned char y);
extern void LcdOpenCursor();
extern void LcdCloseCursor();

void main(){
    unsigned char psec=0xAA; //秒备份，初值 AA 确保首次读取时间后会刷新显示
    EA = 1; //开总中断
    ConfigTimer0(1); //T0 定时 1ms
    InitDS1302(); //初始化实时时钟
    InitLcd1602(); //初始化液晶

    //初始化屏幕上固定不变的内容
    LcdShowStr(3, 0, "20 - - ");
    LcdShowStr(4, 1, " : : ");

    while (1){
        KeyDriver(); //调用按键驱动
        if (flag200ms && (setIndex == 0)){ //每隔 200ms 且未处于设置状态时，
            flag200ms = 0;
            GetRealTime(&bufTime); //获取当前时间
            if (psec != bufTime.sec){ //检测到时间有变化时刷新显示
                RefreshTimeShow();
                psec = bufTime.sec; //用当前值更新上次秒数
            }
        }
    }
}
/* 将一个 BCD 码字节显示到屏幕上，(x,y)-屏幕起始坐标，bcd-待显示 BCD 码 */
void ShowBcdByte(unsigned char x, unsigned char y, unsigned char bcd){
    unsigned char str[4];

    str[0] = (bcd >> 4) + '0';
    str[1] = (bcd&0x0F) + '0';
    str[2] = '\0';
    LcdShowStr(x, y, str);
}
/* 刷新日期时间的显示 */
void RefreshTimeShow(){
    ShowBcdByte(5, 0, bufTime.year);
    ShowBcdByte(8, 0, bufTime.mon);
    ShowBcdByte(11, 0, bufTime.day);
    ShowBcdByte(4, 1, bufTime.hour);
    ShowBcdByte(7, 1, bufTime.min);
    ShowBcdByte(10, 1, bufTime.sec);
}
/* 刷新当前设置位的光标指示 */
void RefreshSetShow(){
    switch (setIndex){
        case 1: LcdSetCursor(5, 0); break;
        case 2: LcdSetCursor(6, 0); break;
        case 3: LcdSetCursor(8, 0); break;
        case 4: LcdSetCursor(9, 0); break;
        case 5: LcdSetCursor(11, 0); break;
        case 6: LcdSetCursor(12, 0); break;
        case 7: LcdSetCursor(4, 1); break;
        case 8: LcdSetCursor(5, 1); break;
        case 9: LcdSetCursor(7, 1); break;
        case 10: LcdSetCursor(8, 1); break;
        case 11: LcdSetCursor(10, 1); break;
        case 12: LcdSetCursor(11, 1); break;
        default: break;
    }
}
/* 递增一个 BCD 码的高位 */
unsigned char IncBcdHigh(unsigned char bcd){
    if ((bcd&0xF0) < 0x90){
        bcd += 0x10;
    }else{
        bcd &= 0x0F;
    }
    return bcd;
}
/* 递增一个 BCD 码的低位 */
unsigned char IncBcdLow(unsigned char bcd){
    if ((bcd&0x0F) < 0x09){
        bcd += 0x01;
    }else{
        bcd &= 0xF0;
    }
    return bcd;
}
/* 递减一个 BCD 码的高位 */
unsigned char DecBcdHigh(unsigned char bcd){
    if ((bcd&0xF0) > 0x00){
        bcd -= 0x10;
    }else{
        bcd |= 0x90;
    }
    return bcd;
}
/* 递减一个 BCD 码的低位 */
unsigned char DecBcdLow(unsigned char bcd){
    if ((bcd&0x0F) > 0x00){
        bcd -= 0x01;
    }else{
        bcd |= 0x09;
    }
    return bcd;
}
/* 递增时间当前设置位的值 */
void IncSetTime(){
    switch (setIndex){
        case 1: bufTime.year = IncBcdHigh(bufTime.year); break;
        case 2: bufTime.year = IncBcdLow(bufTime.year); break;
        case 3: bufTime.mon = IncBcdHigh(bufTime.mon); break;
        case 4: bufTime.mon = IncBcdLow(bufTime.mon); break;
        case 5: bufTime.day = IncBcdHigh(bufTime.day); break;
        case 6: bufTime.day = IncBcdLow(bufTime.day); break;
        case 7: bufTime.hour = IncBcdHigh(bufTime.hour); break;
        case 8: bufTime.hour = IncBcdLow(bufTime.hour); break;
        case 9: bufTime.min = IncBcdHigh(bufTime.min); break;
        case 10: bufTime.min = IncBcdLow(bufTime.min); break;
        case 11: bufTime.sec = IncBcdHigh(bufTime.sec); break;
        case 12: bufTime.sec = IncBcdLow(bufTime.sec); break;
        default: break;
    }
    RefreshTimeShow();
    RefreshSetShow();
}
/* 递减时间当前设置位的值 */
void DecSetTime(){
    switch (setIndex){
        case 1: bufTime.year = DecBcdHigh(bufTime.year); break;
        case 2: bufTime.year = DecBcdLow(bufTime.year); break;
        case 3: bufTime.mon = DecBcdHigh(bufTime.mon); break;
        case 4: bufTime.mon = DecBcdLow(bufTime.mon); break;
        case 5: bufTime.day = DecBcdHigh(bufTime.day); break;
        case 6: bufTime.day = DecBcdLow(bufTime.day); break;
        case 7: bufTime.hour = DecBcdHigh(bufTime.hour); break;
        case 8: bufTime.hour = DecBcdLow(bufTime.hour); break;
        case 9: bufTime.min = DecBcdHigh(bufTime.min); break;
        case 10: bufTime.min = DecBcdLow(bufTime.min); break;
        case 11: bufTime.sec = DecBcdHigh(bufTime.sec); break;
        case 12: bufTime.sec = DecBcdLow(bufTime.sec);
        default: break;
    }
    RefreshTimeShow();
    RefreshSetShow();
}
/* 右移时间设置位 */
void RightShiftTimeSet(){
    if (setIndex != 0){
        if (setIndex < 12){
            setIndex++;
        }else{
            setIndex = 1;
        }
        RefreshSetShow();
    }
}
/* 左移时间设置位 */
void LeftShiftTimeSet(){
    if (setIndex != 0){
        if (setIndex > 1){
            setIndex--;
        }else{
            setIndex = 12;
        }
        RefreshSetShow();
    }
}
/* 进入时间设置状态 */
void EnterTimeSet(){
    setIndex = 2; //把设置索引设置为 2，即可进入设置状态
    LeftShiftTimeSet(); //再利用现成的左移操作移到位置 1 并完成显示刷新
    LcdOpenCursor(); //打开光标闪烁效果
}
/* 退出时间设置状态，save-是否保存当前设置的时间值 */
void ExitTimeSet(bit save){
    setIndex = 0; //把设置索引设置为 0，即可退出设置状态
    if (save){ //需保存时即把当前设置时间写入 DS1302
        SetRealTime(&bufTime);
    }
    LcdCloseCursor(); //关闭光标显示
}
/* 按键动作函数，根据键码执行相应的操作，keycode-按键键码 */
void KeyAction(unsigned char keycode){
    if ((keycode>='0') && (keycode<='9')){ //本例中不响应字符键
    }else if (keycode == 0x26){ //向上键，递增当前设置位的值
        IncSetTime();
    }else if (keycode == 0x28){ //向下键，递减当前设置位的值
        DecSetTime();
    }else if (keycode == 0x25){ //向左键，向左切换设置位
        LeftShiftTimeSet();
    }else if (keycode == 0x27){ //向右键，向右切换设置位
        RightShiftTimeSet();
    }else if (keycode == 0x0D){ //回车键，进入设置模式/启用当前设置值
        if (setIndex == 0){ //不处于设置状态时，进入设置状态
            EnterTimeSet();
        }else{ //已处于设置状态时，保存时间并退出设置状态
            ExitTimeSet(1);
        }
    }else if (keycode == 0x1B){ //Esc 键，取消当前设置
        ExitTimeSet(0);
    }
}
/* 配置并启动 T0，ms-T0 定时时间 */
void ConfigTimer0(unsigned int ms){
    unsigned long tmp; //临时变量
    tmp = 11059200 / 12; //定时器计数频率
    tmp = (tmp * ms) / 1000; //计算所需的计数值
    tmp = 65536 - tmp; //计算定时器重载值
    tmp = tmp + 28; //补偿中断响应延时造成的误差
    T0RH = (unsigned char)(tmp>>8); //定时器重载值拆分为高低字节
    T0RL = (unsigned char)tmp;
    TMOD &= 0xF0; //清零 T0 的控制位
    TMOD |= 0x01; //配置 T0 为模式 1
    TH0 = T0RH; //加载 T0 重载值
    TL0 = T0RL;
    ET0 = 1; //使能 T0 中断
    TR0 = 1; //启动 T0
}
/* T0 中断服务函数，执行按键扫描和 200ms 定时 */
void InterruptTimer0() interrupt 1{
    static unsigned char tmr200ms = 0;
    TH0 = T0RH; //重新加载重载值
    TL0 = T0RL;
    KeyScan(); //按键扫描
    tmr200ms++;
    if (tmr200ms >= 200){ //定时 200ms
        tmr200ms = 0;
        flag200ms = 1;
    }
}
```

main.c 主文件，负责所有应用层的功能实现，文件比较长，还是那句话“不难但比较烦琐”，希望对具体问题分析细化能力还不太强的同学们把这个文件多练习几遍，学习一下其中把具体问题逐步细化并一步步实现出来的编程思想，多进行此类练习，锻炼程序思维能力，将来遇到具体项目设计需求的时候，你很快就可以找到方法并实现它们了。