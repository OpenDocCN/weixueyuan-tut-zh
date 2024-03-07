# 单片机长短按键的应用

在单片机系统中应用按键的时候，如果只需要按下一次按键加 1 或减 1，那用第八章学到的知识就可以完成了，但如果想连续加很多数字的时候，要一次次按下这个按键确实有点不方便，这时我们会希望一直按住按键，数字就自动持续增加或减小，这就是所谓的长短按键应用。

当检测到一个按键产生按下动作后，马上执行一次相应的操作，同时在程序里记录按键按下的持续时间，该时间超过 1 秒后（主要是为了区别短按和长按这两个动作，因短按的时间通常都达到几百 ms），每隔 200ms（如果你需要更快那就用更短的时间，反之亦然）就自动再执行一次该按键对应的操作，这就是一个典型的长按键效果。

对此，我们做了一个模拟定时炸弹效果的实例，提供给大家作为参考。打开开关后，数码管显示数字 0，按向上的按键数字加 1，按向下的按键数字减 1，长按向上按键 1 秒后，数字会持续增加，长按向下按键 1 秒后，数字会持续减小。设定好数字后，按下回车按键，时间就会进行倒计时，当倒计时到 0 的时候，用蜂鸣器和板子上的 8 个 LED 小灯做炸弹效果，蜂鸣器持续响，LED 小灯全亮。

```
#include <reg52.h>

sbit BUZZ = P1⁶;
sbit ADDR3 = P1³;
sbit ENLED = P1⁴;
sbit KEY_IN_1 = P2⁴;
sbit KEY_IN_2 = P2⁵;
sbit KEY_IN_3 = P2⁶;
sbit KEY_IN_4 = P2⁷;
sbit KEY_OUT_1 = P2³;
sbit KEY_OUT_2 = P2²;
sbit KEY_OUT_3 = P2¹;
sbit KEY_OUT_4 = P2⁰;

unsigned char code LedChar[] = { //数码管显示字符转换表
    0xC0, 0xF9, 0xA4, 0xB0, 0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83, 0xC6, 0xA1, 0x86, 0x8E
};
unsigned char LedBuff[7] = { //数码管+独立 LED 显示缓冲区
    0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF
};
unsigned char code KeyCodeMap[4][4] = { //矩阵按键编号到标准键盘键码的映射表
    { 0x31, 0x32, 0x33, 0x26 }, //数字键 1、数字键 2、数字键 3、向上键
    { 0x34, 0x35, 0x36, 0x25 }, //数字键 4、数字键 5、数字键 6、向左键
    { 0x37, 0x38, 0x39, 0x28 }, //数字键 7、数字键 8、数字键 9、向下键
    { 0x30, 0x1B, 0x0D, 0x27 } //数字键 0、ESC 键、 回车键、 向右键
};
unsigned char KeySta[4][4] = { //全部矩阵按键的当前状态
    {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
};
unsigned long pdata KeyDownTime[4][4] = { //每个按键按下的持续时间，单位 ms
    {0, 0, 0, 0}, {0, 0, 0, 0}, {0, 0, 0, 0}, {0, 0, 0, 0}
};

bit enBuzz = 0; //蜂鸣器使能标志
bit flag1s = 0; //1 秒定时标志
bit flagStart = 0; //倒计时启动标志
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节
unsigned int CountDown = 0; //倒计时计数器

void ConfigTimer0(unsigned int ms);
void ShowNumber(unsigned long num);
void KeyDriver();

void main(){
    EA = 1; //使能总中断
    ENLED = 0; //选择数码管和独立 LED
    ADDR3 = 1;
    ConfigTimer0(1); //配置 T0 定时 1ms
    ShowNumber(0); //上电显示 0

    while (1){
        KeyDriver(); //调用按键驱动函数
        if (flagStart && flag1s){ //倒计时启动且 1 秒定时到达时，处理倒计时
            flag1s = 0;
            if (CountDown > 0){ //倒计时未到 0 时，计数器递减
                CountDown--;
                ShowNumber(CountDown); //刷新倒计时数显示
                if (CountDown == 0){ //减到 0 时，执行声光报警
                    enBuzz = 1;
                    //启动蜂鸣器发声
                    LedBuff[6] = 0x00; //点亮独立 LED
                }
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
    /* 将一个无符号长整型的数字显示到数码管上，num-待显示数字 */
void ShowNumber(unsigned long num){
    signed char i;
    unsigned char buf[6];
    for (i=0; i<6; i++){ //把长整型数转换为 6 位十进制的数组
        buf[i] = num % 10;
        num = num / 10;
    }
    for (i=5; i>=1; i--){ //从最高位起，遇到 0 转换为空格，遇到非 0 则退出循环
        if (buf[i] == 0){
            LedBuff[i] = 0xFF;
        }else{
            break;
        }
    }

    for ( ; i>=0; i--){ //剩余低位都如实转换为数码管显示字符
        LedBuff[i] = LedChar[buf[i]];
    }
}
/* 按键动作函数，根据键码执行相应的操作，keycode-按键键码 */
void KeyAction(unsigned char keycode){ //按键动作函数，根据键码执行相应动作
    if (keycode == 0x26){ //向上键，倒计时设定值递增
        if (CountDown < 9999){ //最大计时 9999 秒
            CountDown++;
            ShowNumber(CountDown);
        }
    }else if (keycode == 0x28){ //向下键，倒计时设定值递减
        if (CountDown > 1){ //最小计时 1 秒
            CountDown--;
            ShowNumber(CountDown);
        }
    }else if (keycode == 0x0D){ //回车键，启动倒计时
        flagStart = 1; //启动倒计时
    }else if (keycode == 0x1B){ //Esc 键，取消倒计时
        enBuzz = 0; //关闭蜂鸣器
        LedBuff[6] = 0xFF; //关闭独立 LED
        flagStart = 0; //停止倒计时
        CountDown = 0; //倒计时数归零
        ShowNumber(CountDown);
    }
}
/* 按键驱动函数，检测按键动作，调度相应动作函数，需在主循环中调用 */
void KeyDriver(){
    unsigned char i, j;
    static unsigned char pdata backup[4][4] = { //按键值备份，保存前一次的值
        {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
    };
    static unsigned long pdata TimeThr[4][4] = { //快速输入执行的时间阈值
        {1000, 1000, 1000, 1000}, {1000, 1000, 1000, 1000},
        {1000, 1000, 1000, 1000}, {1000, 1000, 1000, 1000}
    };

    for (i=0; i<4; i++){ //循环扫描 4*4 的矩阵按键
        for (j=0; j<4; j++){
            if (backup[i][j] != KeySta[i][j]){ //检测按键动作
                if (backup[i][j] != 0){ //按键按下时执行动作
                    KeyAction(KeyCodeMap[i][j]); //调用按键动作函数
                }
            }
            backup[i][j] = KeySta[i][j];
            if (KeyDownTime[i][j] > 0){
                if (KeyDownTime[i][j] >= TimeThr[i][j]){ //达到阈值时执行一次动作
                    KeyAction(KeyCodeMap[i][j]); //调用按键动作函数
                    TimeThr[i][j] += 200; //时间阈值增加 200ms，以准备下次执行
                }
            }else{ //按键弹起时复位阈值时间
                TimeThr[i][j] = 1000; //恢复 1s 的初始阈值时间
            }
        }
    }
}
/* 按键扫描函数，需在定时中断中调用 */
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
        //连续 4 次扫描值为 0，即 4*4ms 内都是按下状态时，可认为按键已稳定的按下
        if ((keybuf[keyout][i] & 0x0F) == 0x00){
            KeySta[keyout][i] = 0;
            KeyDownTime[keyout][i] += 4; //按下的持续时间累加
        //连续 4 次扫描值为 1，即 4*4ms 内都是弹起状态时，可认为按键已稳定的弹起
        }else if ((keybuf[keyout][i] & 0x0F) == 0x0F){
            KeySta[keyout][i] = 1;
        }
    }
    KeyDownTime[keyout][i] = 0; //按下的持续时间清零
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
/* LED 动态扫描刷新函数，需在定时中断中调用 */
void LedScan(){
    static unsigned char i = 0; //动态扫描索引
    P0 = 0xFF; //关闭所有段选位，显示消隐
    P1 = (P1 & 0xF8) | i; //位选索引值赋值到 P1 口低 3 位
    P0 = LedBuff[i]; //缓冲区中索引位置的数据送到 P0 口
    if (i < 6){ //索引递增循环，遍历整个缓冲区
        i++;
    }else{
        i = 0;
    }
}
/* T0 中断服务函数，完成数码管、按键扫描与秒定时 */
void InterruptTimer0() interrupt 1{
    static unsigned int tmr1s = 0; //1 秒定时器
    TH0 = T0RH; //重新加载重载值
    TL0 = T0RL;
    if (enBuzz){ //蜂鸣器发声处理
        BUZZ = ~BUZZ; //驱动蜂鸣器发声
    }else{
        BUZZ = 1; //关闭蜂鸣器
    }
    LedScan(); //LED 扫描显示
    KeyScan(); //按键扫描
    if (flagStart){ //倒计时启动时处理 1 秒定时
        tmr1s++;
        if (tmr1s >= 1000){
            tmr1s = 0;
            flag1s = 1;
        }
    }else{ //倒计时未启动时 1 秒定时器始终归零
        tmr1s = 0;
    }
}
```

长按键功能实现的重点有两个：第一，是在原来的矩阵按键扫描函数 KeyScan 内，当检测到按键按下后，持续的对一个时间变量进行累加，其目的是用这个时间变量来记录按键按下的时间；第二，是在按键驱动函数 KeyDriver 里，除了原来的检测到按键按下这个动作时执行按键动作函数 KeyAction 外，还监测表示按键按下时间的变量，根据它的值来完成长按时的连续快速按键动作功能。