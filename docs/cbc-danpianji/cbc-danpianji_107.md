# 单片机 EEPROM 单字节读写操作时序

## EEPROM 写数据流程

第一步，首先是 I²C 的起始信号，接着跟上首字节，也就是我们前边讲的 I²C 的器件地址，并且在读写方向上选择“写”操作。

第二步，发送数据的存储地址。24C02 一共 256 个字节的存储空间，地址从 0x00～0xFF，我们想把数据存储在哪个位置，此刻写的就是哪个地址。

第三步，发送要存储的数据第一个字节、第二个字节„„注意在写数据的过程中，EEPROM 每个字节都会回应一个“应答位 0”，来告诉我们写 EEPROM 数据成功，如果没有回应答位，说明写入不成功。

在写数据的过程中，每成功写入一个字节，EEPROM 存储空间的地址就会自动加 1，当加到 0xFF 后，再写一个字节，地址会溢出又变成了 0x00。

## EEPROM 读数据流程

第一步，首先是 I²C 的起始信号，接着跟上首字节，也就是我们前边讲的 I²C 的器件地址，并且在读写方向上选择“写”操作。这个地方可能有同学会诧异，我们明明是读数据为何方向也要选“写”呢？刚才说过了，24C02 一共有 256 个地址，我们选择写操作，是为了把所要读的数据的存储地址先写进去，告诉 EEPROM 我们要读取哪个地址的数据。这就如同我们打电话，先拨总机号码（EEPROM 器件地址），而后还要继续拨分机号码（数据地址），而拨分机号码这个动作，主机仍然是发送方，方向依然是“写”。

第二步，发送要读取的数据的地址，注意是地址而非存在 EEPROM 中的数据，通知 EEPROM 我要哪个分机的信息。

第三步，重新发送 I²C 起始信号和器件地址，并且在方向位选择“读”操作。

这三步当中，每一个字节实际上都是在“写”，所以每一个字节 EEPROM 都会回应一个“应答位 0”。

第四步，读取从器件发回的数据，读一个字节，如果还想继续读下一个字节，就发送一个“应答位 ACK(0)”，如果不想读了，告诉 EEPROM，我不想要数据了，别再发数据了，那就发送一个“非应答位 NAK(1)”。

和写操作规则一样，我们每读一个字节，地址会自动加 1，那如果我们想继续往下读，给 EEPROM 一个 ACK(0)低电平，那再继续给 SCL 完整的时序，EEPROM 会继续往外送数据。如果我们不想读了，要告诉 EEPROM 不要数据了，那我们直接给一个 NAK(1)高电平即可。这个地方大家要从逻辑上理解透彻，不能简单的靠死记硬背了，一定要理解明白。梳理一下几个要点：

A、在本例中单片机是主机，24C02 是从机；

B、无论是读是写，SCL 始终都是由主机控制的；

C、写的时候应答信号由从机给出，表示从机是否正确接收了数据；

D、读的时候应答信号则由主机给出，表示是否继续读下去。

那我们下面写一个程序，读取 EEPROM 的 0x02 这个地址上的一个数据，不管这个数据之前是多少，我们都将读出来的数据加 1，再写到 EEPROM 的 0x02 这个地址上。此外我们将 I²C 的程序建立一个文件，写一个 I2C.c 程序文件，形成我们又一个程序模块。大家也可以看出来，我们连续的这几个程序，Lcd1602.c 文件里的程序都是一样的，今后我们大家写 1602 显示程序也可以直接拿过去用，大大提高了程序移植的方便性。

```
/******************************I2C.c 文件程序源代码******************************/
#include <reg52.h>
#include <intrins.h>
#define I2CDelay() {_nop_();_nop_();_nop_();_nop_();}
sbit I2C_SCL = P3⁷;
sbit I2C_SDA = P3⁶;
/* 产生总线起始信号 */
void I2CStart(){
    I2C_SDA = 1; //首先确保 SDA、SCL 都是高电平
    I2C_SCL = 1;
    I2CDelay();
    I2C_SDA = 0; //先拉低 SDA
    I2CDelay();
    I2C_SCL = 0; //再拉低 SCL
}
/* 产生总线停止信号 */
void I2CStop(){
    I2C_SCL = 0; //首先确保 SDA、SCL 都是低电平
    I2C_SDA = 0;
    I2CDelay();
    I2C_SCL = 1; //先拉高 SCL
    I2CDelay();
    I2C_SDA = 1; //再拉高 SDA
    I2CDelay();
}
/* I2C 总线写操作，dat-待写入字节，返回值-从机应答位的值 */
bit I2CWrite(unsigned char dat){
    bit ack; //用于暂存应答位的值
    unsigned char mask; //用于探测字节内某一位值的掩码变量

    for (mask=0x80; mask!=0; mask>>=1){ //从高位到低位依次进行
        if ((mask&dat) == 0){ //该位的值输出到 SDA 上
            I2C_SDA = 0;
        }else{
            I2C_SDA = 1;
        }
        I2CDelay();
        I2C_SCL = 1; //拉高 SCL
        I2CDelay();
        I2C_SCL = 0; //再拉低 SCL，完成一个位周期
    }

    I2C_SDA = 1; //8 位数据发送完后，主机释放 SDA，以检测从机应答
    I2CDelay();
    I2C_SCL = 1; //拉高 SCL
    ack = I2C_SDA; //读取此时的 SDA 值，即为从机的应答值
    I2CDelay();
    I2C_SCL = 0; //再拉低 SCL 完成应答位，并保持住总线
    //应答值取反以符合通常的逻辑：
    //0=不存在或忙或写入失败，1=存在且空闲或写入成功
    return (~ack);
}
/* I2C 总线读操作，并发送非应答信号，返回值-读到的字节 */
unsigned char I2CReadNAK(){
    unsigned char mask;
    unsigned char dat;

    I2C_SDA = 1; //首先确保主机释放 SDA
    for (mask=0x80; mask!=0; mask>>=1){ //从高位到低位依次进行
        I2CDelay();
        I2C_SCL = 1; //拉高 SCL
        if(I2C_SDA == 0){ //读取 SDA 的值
            dat &= ~mask; //为 0 时，dat 中对应位清零
        }else{
            dat |= mask; //为 1 时，dat 中对应位置 1
        }
        I2CDelay();
        I2C_SCL = 0; //再拉低 SCL，以使从机发送出下一位
    }
    I2C_SDA = 1; //8 位数据发送完后，拉高 SDA，发送非应答信号
    I2CDelay();
    I2C_SCL = 1; //拉高 SCL
    I2CDelay();
    I2C_SCL = 0; //再拉低 SCL 完成非应答位，并保持住总线
    return dat;
}

/* I2C 总线读操作，并发送应答信号，返回值-读到的字节 */
unsigned char I2CReadACK(){
    unsigned char mask;
    unsigned char dat;

    I2C_SDA = 1; //首先确保主机释放 SDA
    for (mask=0x80; mask!=0; mask>>=1){ //从高位到低位依次进行
        I2CDelay();
        I2C_SCL = 1; //拉高 SCL
        if(I2C_SDA == 0){ //读取 SDA 的值
            dat &= ~mask; //为 0 时，dat 中对应位清零
        }else{
            dat |= mask; //为 1 时，dat 中对应位置 1
        }
        I2CDelay();
        I2C_SCL = 0; //再拉低 SCL，以使从机发送出下一位
    }
    I2C_SDA = 0; //8 位数据发送完后，拉低 SDA，发送应答信号
    I2CDelay();
    I2C_SCL = 1; //拉高 SCL
    I2CDelay();
    I2C_SCL = 0; //再拉低 SCL 完成应答位，并保持住总线
    return dat;
}
```

I2C.c 文件提供了 I²C 总线所有的底层操作函数，包括起始、停止、字节写、字节读+应答、字节读+非应答。

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
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);
extern void I2CStart();
extern void I2CStop();
extern unsigned char I2CReadNAK();
extern bit I2CWrite(unsigned char dat);
unsigned char E2ReadByte(unsigned char addr);
void E2WriteByte(unsigned char addr, unsigned char dat);

void main(){
    unsigned char dat;
    unsigned char str[10];

    InitLcd1602(); //初始化液晶
    dat = E2ReadByte(0x02); //读取指定地址上的一个字节
    str[0] = (dat/100) + '0'; //转换为十进制字符串格式
    str[1] = (dat/10%10) + '0';
    str[2] = (dat%10) + '0';
    str[3] = '\0';

    LcdShowStr(0, 0, str); //显示在液晶上
    dat++; //将其数值+1
    E2WriteByte(0x02, dat); //再写回到对应的地址上
    while (1);
}

/* 读取 EEPROM 中的一个字节，addr-字节地址 */
unsigned char E2ReadByte(unsigned char addr){
    unsigned char dat;

    I2CStart();
    I2CWrite(0x50<<1); //寻址器件，后续为写操作
    I2CWrite(addr); //写入存储地址
    I2CStart(); //发送重复启动信号
    I2CWrite((0x50<<1)|0x01); //寻址器件，后续为读操作
    dat = I2CReadNAK(); //读取一个字节数据
    I2CStop();
    return dat;
}
/* 向 EEPROM 中写入一个字节，addr-字节地址 */
void E2WriteByte(unsigned char addr, unsigned char dat){
    I2CStart();
    I2CWrite(0x50<<1); //寻址器件，后续为写操作
    I2CWrite(addr); //写入存储地址
    I2CWrite(dat); //写入一个字节数据
    I2CStop();
}
```

这个程序，以同学们现在的基础，独立分析应该不困难了，遇到哪个语句不懂可以及时问问别人或者搜索一下，把该解决的问题理解明白。大家把这个程序复制过去后，编译一下会发现 Keil 软件提示了一个警告：*** WARNING L16: UNCALLED SEGMENT, IGNORED FOR OVERLAY PROCESS，这个警告的意思是在代码中存在没有被调用过的变量或者函数，即 I2C.c 文件中的 I2CReadACK()这个函数在本例中没有用到。

大家仔细观察一下这个程序，我们读取 EEPROM 的时候，只读了一个字节就要告诉 EEPROM 不需要再读数据了，读完后直接发送一个“NAK”，因此只调用了 I2CReadNAK()这个函数，而并没有调用 I2CReadACK()这个函数。我们今后很可能读数据的时候要连续读几个字节，因此这个函数写在了 I2C.c 文件中，作为 I²C 功能模块的一部分是必要的，方便我们这个文件以后移植到其他程序中使用，因此这个警告在这里就不必管它了。