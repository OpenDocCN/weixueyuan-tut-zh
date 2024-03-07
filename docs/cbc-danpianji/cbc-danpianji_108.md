# 单片机 EEPROM 多字节读写操作时序

我们读取 EEPROM 的时候很简单，EEPROM 根据我们所送的时序，直接就把数据送出来了，但是写 EEPROM 却没有这么简单了。给 EEPROM 发送数据后，先保存在了 EEPROM 的缓存，EEPROM 必须要把缓存中的数据搬移到“非易失”的区域，才能达到掉电不丢失的效果。而往非易失区域写需要一定的时间，每种器件不完全一样，ATMEL 公司的 24C02 的这个写入时间最高不超过 5ms。在往非易失区域写的过程，EEPROM 是不会再响应我们的访问的，不仅接收不到我们的数据，我们即使用 I²C 标准的寻址模式去寻址，EEPROM 都不会应答，就如同这个总线上没有这个器件一样。数据写入非易失区域完毕后，EEPROM 再次恢复正常，可以正常读写了。

细心的同学，在看上一节程序的时候会发现，我们写数据的那段代码，实际上我们有去读应答位 ACK，但是读到了应答位我们也没有做任何处理。这是因为我们一次只写一个字节的数据进去，等到下次重新上电再写的时候，时间肯定远远超过了 5ms，但是如果我们是连续写入几个字节的时候，就必须得考虑到应答位的问题了。写入一个字节后，再写入下一个字节之前，我们必须要等待 EEPROM 再次响应才可以，大家注意我们程序的写法，可以学习一下。

之前我们知道编写多.c 文件移植的方便性了，本节程序和上一节的 Lcd1602.c 文件和 I2C.c 文件完全是一样的，因此这次我们只把 main.c 文件给大家发出来，帮大家分析明白。

而同学们却不能这样，同学们是初学，很多知识和技巧需要多练才能巩固下来，因此每个程序还是建议大家在你的 Keil 软件上一个代码一个代码的敲出来。

/*****************************I2C.c 文件程序源代码*******************************/

（此处省略，可参考之前章节的代码）

/***************************Lcd1602.c 文件程序源代码*****************************/

（此处省略，可参考之前章节的代码）

```
/*****************************main.c 文件程序源代码******************************/
#include <reg52.h>
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);
extern void I2CStart();
extern void I2CStop();
extern unsigned char I2CReadACK();
extern unsigned char I2CReadNAK();
extern bit I2CWrite(unsigned char dat);
void E2Read(unsigned char *buf, unsigned char addr, unsigned char len);
void E2Write(unsigned char *buf, unsigned char addr, unsigned char len);
void MemToStr(unsigned char *str, unsigned char *src, unsigned char len);

void main(){
    unsigned char i;
    unsigned char buf[5];
    unsigned char str[20];
    InitLcd1602(); //初始化液晶
    E2Read(buf, 0x90, sizeof(buf)); //从 E2 中读取一段数据
    MemToStr(str, buf, sizeof(buf)); //转换为十六进制字符串
    LcdShowStr(0, 0, str); //显示到液晶上
    for (i=0; i<sizeof(buf); i++){ //数据依次+1,+2,+3...
        buf[i] = buf[i] + 1 + i;
    }
    E2Write(buf, 0x90, sizeof(buf)); //再写回到 E2 中
    while(1);
}
/* 将一段内存数据转换为十六进制格式的字符串，
str-字符串指针，src-源数据地址，len-数据长度 */
void MemToStr(unsigned char *str, unsigned char *src, unsigned char len){
    unsigned char tmp;

    while (len--){
        tmp = *src >> 4; //先取高 4 位
        if (tmp <= 9){ //转换为 0-9 或 A-F
            *str++ = tmp + '0';
        }else{
            *str++ = tmp - 10 + 'A';
        }
        tmp = *src & 0x0F; //再取低 4 位
        if (tmp <= 9){ //转换为 0-9 或 A-F
            *str++ = tmp + '0';
        }else{
            *str++ = tmp - 10 + 'A';
        }
        *str++ = ' '; //转换完一个字节添加一个空格
        src++;
    }
}
/* E2 读取函数，buf-数据接收指针，addr-E2 中的起始地址，len-读取长度 */
void E2Read(unsigned char *buf, unsigned char addr, unsigned char len){
    do { //用寻址操作查询当前是否可进行读写操作
        I2CStart();
        if (I2CWrite(0x50<<1)){ //应答则跳出循环，非应答则进行下一次查询
            break;
        }
        I2CStop();
    } while(1);
    I2CWrite(addr); //写入起始地址
    I2CStart(); //发送重复启动信号
    I2CWrite((0x50<<1)|0x01); //寻址器件，后续为读操作
    while (len > 1){ //连续读取 len-1 个字节
        *buf++ = I2CReadACK(); //最后字节之前为读取操作+应答
        len--;
    }
    *buf = I2CReadNAK(); //最后一个字节为读取操作+非应答
    I2CStop();
}
/* E2 写入函数，buf-源数据指针，addr-E2 中的起始地址，len-写入长度 */
void E2Write(unsigned char *buf, unsigned char addr, unsigned char len){
    while (len--){
        do { //用寻址操作查询当前是否可进行读写操作
            I2CStart();
            if (I2CWrite(0x50<<1)){ //应答则跳出循环，非应答则进行下一次查询
                break;
            }
            I2CStop();
        } while(1);
        I2CWrite(addr++); //写入起始地址
        I2CWrite(*buf++); //写入一个字节数据
        I2CStop(); //结束写操作，以等待写入完成
    }
}
```

函数 MemToStr：可以把一段内存数据转换成十六进制字符串的形式。由于我们从 EEPROM 读出来的是正常的数据，而 1602 液晶接收的是 ASCII 码字符，因此我们要通过液晶把数据显示出来必须先通过一步转换。算法倒是很简单，就是把每一个字节的数据高 4 位和低 4 位分开，和 9 进行比较，如果小于等于 9，则直接加„0‟转为 0～9 的 ASCII 码；如果大于 9，则先减掉 10 再加„A‟即可转为 A～F 的 ASCII 码。

函数 E2Read：我们在读之前，要查询一下当前是否可以进行读写操作，EEPROM 正常响应才可以进行。进行后，读最后一个字节之前的，全部给出 ACK，而读完了最后一个字节，我们要给出一个 NAK。

函数 E2Write：每次写操作之前，我们都要进行查询判断当前 EEPROM 是否响应，正常响应后才可以写数据。