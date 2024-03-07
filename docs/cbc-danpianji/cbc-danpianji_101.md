# 单片机计算器程序设计[详细]

按键和液晶，可以组成我们最简易的计算器。下面我们来写一个简易整数计算器提供给大家学习。为了让程序不过于复杂，我们这个计算器不考虑连加，连减等连续计算，不考虑小数情况。加减乘除分别用上下左右来替代，回车表示等于，ESC 表示归 0。程序共分为三部分，一部分是 1602 液晶显示，一部分是按键动作和扫描，一部分是主函数功能。

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
/* 整屏清除 */
void LcdFullClear(){
    LcdWriteCmd(0x01);
}
/* 初始化 1602 液晶 */
void InitLcd1602(){
    LcdWriteCmd(0x38); //16*2 显示，5*7 点阵，8 位数据接口
    LcdWriteCmd(0x0C); //显示器开，光标关闭
    LcdWriteCmd(0x06); //文字不动，地址自动+1
    LcdWriteCmd(0x01); //清屏
}

```

Lcd1602.c 文件中根据上层应用的需要增加了 2 个清屏函数：区域清屏——LcdAreaClear，整屏清屏——LcdFullClear。

```
/**************************keyboard.c 文件程序源代码*****************************/
#include <reg52.h>
sbit KEY_IN_1 = P2⁴;
sbit KEY_IN_2 = P2⁵;
sbit KEY_IN_3 = P2⁶;
sbit KEY_IN_4 = P2⁷;
sbit KEY_OUT_1 = P2³;
sbit KEY_OUT_2 = P2²;
sbit KEY_OUT_3 = P2¹;
sbit KEY_OUT_4 = P2⁰;

unsigned char code KeyCodeMap[4][4] = { //矩阵按键编号到标准键盘键码的映射表
    { '1', '2', '3', 0x26 }, //数字键 1、数字键 2、数字键 3、向上键
    { '4', '5', '6', 0x25 }, //数字键 4、数字键 5、数字键 6、向左键
    { '7', '8', '9', 0x28 }, //数字键 7、数字键 8、数字键 9、向下键
    { '0', 0x1B, 0x0D, 0x27 } //数字键 0、ESC 键、 回车键、 向右键
};
unsigned char pdata KeySta[4][4] = { //全部矩阵按键的当前状态
    {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
};

extern void KeyAction(unsigned char keycode);

/* 按键驱动函数，检测按键动作，调度相应动作函数，需在主循环中调用 */
void KeyDriver(){
    unsigned char i, j;
    static unsigned char pdata backup[4][4] = { //按键值备份，保存前一次的值
        {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
    };

    for (i=0; i<4; i++){ //循环检测 4*4 的矩阵按键
        for (j=0; j<4; j++){
            if (backup[i][j] != KeySta[i][j]){ //检测按键动作
                if (backup[i][j] != 0){ //按键按下时执行动作
                    KeyAction(KeyCodeMap[i][j]); //调用按键动作函数
                }
                backup[i][j] = KeySta[i][j]; //刷新前一次的备份值
            }
        }
    }
}
/* 按键扫描函数，需在定时中断中调用，推荐调用间隔 1ms */
void KeyScan(){
    unsigned char i;
    static unsigned char keyout = 0; //矩阵按键扫描输出索引
    static unsigned char keybuf[4][4] = { //矩阵按键扫描缓冲区
        {0xFF, 0xFF, 0xFF, 0xFF}, {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF}, {0xFF, 0xFF, 0xFF, 0xFF}
    };

    //将一行的 4 个按键值移入缓冲区
    keybuf[keyout][0] = (keybuf[keyout][0] << 1) | KEY_IN_1;
    keybuf[keyout][1] = (keybuf[keyout][1] << 1) | KEY_IN_2;
    keybuf[keyout][2] = (keybuf[keyout][2] << 1) | KEY_IN_3;
    keybuf[keyout][3] = (keybuf[keyout][3] << 1) | KEY_IN_4;
    //消抖后更新按键状态
    for (i=0; i<4; i++){ //每行 4 个按键，所以循环 4 次
        if ((keybuf[keyout][i] & 0x0F) == 0x00){
            //连续 4 次扫描值为 0，即 4*4ms 内都是按下状态时，可认为按键已稳定的按下
            KeySta[keyout][i] = 0;
        }else if ((keybuf[keyout][i] & 0x0F) == 0x0F){
            //连续 4 次扫描值为 1，即 4*4ms 内都是弹起状态时，可认为按键已稳定的弹起
            KeySta[keyout][i] = 1;
        }
    }

    //执行下一次的扫描输出
    keyout++; //输出索引递增
    keyout &= 0x03; //索引值加到 4 即归零
    switch (keyout){ //根据索引，释放当前输出引脚，拉低下次的输出引脚
        case 0: KEY_OUT_4 = 1; KEY_OUT_1 = 0; break;
        case 1: KEY_OUT_1 = 1; KEY_OUT_2 = 0; break;
        case 2: KEY_OUT_2 = 1; KEY_OUT_3 = 0; break;
        case 3: KEY_OUT_3 = 1; KEY_OUT_4 = 0; break;
        default: break;
    }
}
```

keyboard.c 是对之前已经用过多次的矩阵按键驱动的封装，具体到某个按键要执行的动作函数都放到上层的 main.c 中实现，在这个按键驱动文件中只负责调用上层实现的按键动作函数即可。

```
/*****************************main.c 文件程序源代码******************************/
#include <reg52.h>
unsigned char step = 0; //操作步骤
unsigned char oprt = 0; //运算类型
signed long num1 = 0; //操作数 1
signed long num2 = 0; //操作数 2
signed long result = 0; //运算结果
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节

void ConfigTimer0(unsigned int ms);
extern void KeyScan();
extern void KeyDriver();
extern void InitLcd1602();
extern void LcdShowStr(unsigned char x, unsigned char y, unsigned char *str);
extern void LcdAreaClear(unsigned char x, unsigned char y, unsigned char len);
extern void LcdFullClear();

void main(){
    EA = 1; //开总中断
    ConfigTimer0(1); //配置 T0 定时 1ms
    InitLcd1602(); //初始化液晶
    LcdShowStr(15, 1, "0"); //初始显示一个数字 0
    while (1){
        KeyDriver(); //调用按键驱动
    }
}
/* 长整型数转换为字符串，str-字符串指针，dat-待转换数，返回值-字符串长度 */
unsigned char LongToString(unsigned char *str, signed long dat){
    signed char i = 0;
    unsigned char len = 0;
    unsigned char buf[12];

    if (dat < 0){ //如果为负数，首先取绝对值，并在指针上添加负号
        dat = -dat;
        *str++ = '-';
        len++;
    }

    do { //先转换为低位在前的十进制数组
        buf[i++] = dat % 10;
        dat /= 10;
    } while (dat > 0);
    len += i; //i 最后的值就是有效字符的个数
    while (i-- > 0){ //将数组值转换为 ASCII 码反向拷贝到接收指针上
        *str++ = buf[i] + '0';
    }
    *str = '\0'; //添加字符串结束符
    return len; //返回字符串长度
}
/* 显示运算符，显示位置 y，运算符类型 type */
void ShowOprt(unsigned char y, unsigned char type){
    switch (type){
        case 0: LcdShowStr(0, y, "+"); break; //0 代表+
        case 1: LcdShowStr(0, y, "-"); break; //1 代表-
        case 2: LcdShowStr(0, y, "*"); break; //2 代表*
        case 3: LcdShowStr(0, y, "/"); break; //3 代表/
        default: break;
    }
}
/* 计算器复位，清零变量值，清除屏幕显示 */
void Reset(){
    num1 = 0;
    num2 = 0;
    step = 0;
    LcdFullClear();
}
/* 数字键动作函数，n-按键输入的数值 */
void NumKeyAction(unsigned char n){
    unsigned char len;
    unsigned char str[12];

    if (step > 1){ //如计算已完成，则重新开始新的计算
        Reset();
    }
    if (step == 0){ //输入第一操作数
        num1 = num1*10 + n; //输入数值累加到原操作数上
        len = LongToString(str, num1); //新数值转换为字符串
        LcdShowStr(16-len, 1, str); //显示到液晶第二行上
    }else{ //输入第二操作数
        num2 = num2*10 + n; //输入数值累加到原操作数上
        len = LongToString(str, num2); //新数值转换为字符串
        LcdShowStr(16-len, 1, str); //显示到液晶第二行上
    }
}
/* 运算符按键动作函数，运算符类型 type */
void OprtKeyAction(unsigned char type){
    unsigned char len;
    unsigned char str[12];

    if (step == 0){ //第二操作数尚未输入时响应，即不支持连续操作
        len = LongToString(str, num1); //第一操作数转换为字符串
        LcdAreaClear(0, 0, 16-len); //清除第一行左边的字符位
        LcdShowStr(16-len, 0, str); //字符串靠右显示在第一行
        ShowOprt(1, type); //在第二行显示操作符
        LcdAreaClear(1, 1, 14); //清除第二行中间的字符位
        LcdShowStr(15, 1, "0"); //在第二行最右端显示 0
        oprt = type; //记录操作类型
        step = 1;
    }
}
/* 计算结果函数 */
void GetResult(){
    unsigned char len;
    unsigned char str[12];

    if (step == 1){ //第二操作数已输入时才执行计算
        step = 2;
        switch (oprt){ //根据运算符类型计算结果，未考虑溢出问题
            case 0: result = num1 + num2; break;
            case 1: result = num1 - num2; break;
            case 2: result = num1 * num2; break;
            case 3: result = num1 / num2; break;
            default: break;
        }
        len = LongToString(str, num2); //原第二操作数和运算符显示到第一行
        ShowOprt(0, oprt);
        LcdAreaClear(1, 0, 16-1-len);
        LcdShowStr(16-len, 0, str);
        len = LongToString(str, result); //计算结果和等号显示在第二行
        LcdShowStr(0, 1, "=");
        LcdAreaClear(1, 1, 16-1-len);
        LcdShowStr(16-len, 1, str);
    }
}
/* 按键动作函数，根据键码执行相应的操作，keycode-按键键码 */
void KeyAction(unsigned char keycode){
    if ((keycode>='0') && (keycode<='9')){ //输入字符
        NumKeyAction(keycode - '0');
    }else if (keycode == 0x26){ //向上键，+
        OprtKeyAction(0);
    }else if (keycode == 0x28){ //向下键，-
        OprtKeyAction(1);
    }else if (keycode == 0x25){ //向左键，*
        OprtKeyAction(2);
    }else if (keycode == 0x27){ //向右键，÷
        OprtKeyAction(3);
    }else if (keycode == 0x0D){ //回车键，计算结果
        GetResult();
    }else if (keycode == 0x1B){ //Esc 键，清除
        Reset();
        LcdShowStr(15, 1, "0");
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
/* T0 中断服务函数，执行按键扫描 */
void InterruptTimer0() interrupt 1{
    TH0 = T0RH; //重新加载重载值
    TL0 = T0RL;
    KeyScan(); //按键扫描
}
```

main.c 文件实现所有应用层的操作函数，即计算器功能所需要信息显示、按键动作响应等，另外还包括主循环和定时中断的调度。

通过这样一个程序，大家一方面学习如何进行多个.c 文件的编程，另外一个方面学会多个函数之间的灵活调用。可以把这个程序看成是一个简单的小项目，学习一下项目编程都是如何进行和布局的。不要把项目想象的太难，再复杂的项目也是这种简单程序的组合和扩展而已。