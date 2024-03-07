# 单片机简易加法计算器程序

学到这里，我们已经掌握了一种显示设备和一种输入设备的使用，那么是不是可以来做点综合性的实验了。好吧，那我们就来做一个简易的加法计算器，用程序实现从板子上标有 0～9 数字的按键输入相应数字，该数字要实时显示到数码管上，用标有向上箭头的按键代替加号，按下加号后可以再输入一串数字，然后回车键计算加法结果，并同时显示到数码管上。虽然这远不是一个完善的计算器程序，但作为初学者也足够你研究一阵子了。

首先，本程序相对于之前的例程要复杂得多，需要完成的工作也多得多，所以我们把各个子功能都做成独立的函数，以使程序便于编写和维护。大家分析程序的时候就从主函数和中断函数入手，随着程序的流程进行就可以了。大家可以体会体会划分函数的好处，想想如果还是只有主函数和中断函数来实现的话程序会是什么样子。

其次，大家可以看到我们再把矩阵按键扫描分离出动作以后，并没有直接使用行列数所组成的数值作为分支判断执行动作的依据，而是把抽象的行列数转换为了一种叫做标准键盘键码（就是电脑键盘的编码）的数据，然后用得到的这个数据作为下一步分支判断执行动作的依据，为什么多此一举呢？有两层含义：第一，尽量让自己设计的东西（包括硬件和软件）向已有的行业规范或标准看齐，这样有助于别人理解认可你的设计，也有助于你的设计与别人的设计相对接，毕竟标准就是为此而生的嘛。第二，有助于程序的层次化而方便维护与移植，比如我们现在用的按键是 4*4 的，但如果后续又增加了一行成了 4*5 的，那么由行列数组成的编号可能就变了，我们就要在程序的各个分支中查找修改，稍不留神就会出错，而采用这种转换后，我们则只需要维护 KeyCodeMap 这样一个数组表格就行了，看上去就像是把程序的底层驱动与应用层的功能实现函数分离开了，应用层不用关心底层的实现细节，底层改变后也无需在应用层中做相应修改，两层程序之间是一种标准化的接口。这就是程序的层次化，而层次化是构建复杂系统的必备条件，那么现在就先通过简单的示例来学习一下吧。

作为初学者针对这种程序的学习方式是，先从头到尾读一到三遍，边读边理解，然后边抄边理解，彻底理解透彻后，自己尝试独立写出来。完全采用记忆模式来学习这种例程，一两个例程你可能感觉不到什么提高，当这种例程背过上百八十个的时候，厚积薄发的感觉就来了。同时，在抄读的过程中也要注意学习编程规范，这些可都是无形的财富，可以为你日后的研发工作加分的哦。

```
#include <reg52.h>

sbit ADDR0 = P1⁰;
sbit ADDR1 = P1¹;
sbit ADDR2 = P1²;
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
unsigned char LedBuff[6] = { //数码管显示缓冲区
    0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF
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

void KeyDriver();
void main(){
    EA = 1;  //使能总中断
    ENLED = 0;  //选择数码管进行显示
    ADDR3 = 1;
    TMOD = 0x01; //设置 T0 为模式 1
    TH0 = 0xFC; //为 T0 赋初值 0xFC67，定时 1ms
    TL0 = 0x67;
    ET0 = 1; //使能 T0 中断
    TR0 = 1; //启动 T0
    LedBuff[0] = LedChar[0]; //上电显示 0

    while (1){
        KeyDriver();  //调用按键驱动函数
    }
}
/* 将一个无符号长整型的数字显示到数码管上，num-待显示数字 */
void ShowNumber(unsigned long num){
    signed char i;
    unsigned char buf[6];
    //把长整型数转换为 6 位十进制的数组
    for (i=0; i<6; i++){
        buf[i] = num % 10;
        num = num / 10;
    }
    //从最高位起，遇到 0 转换为空格，遇到非 0 则退出循环
    for (i=5; i>=1; i--){
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
void KeyAction(unsigned char keycode){
    static unsigned long result = 0; //用于保存运算结果
    static unsigned long addend = 0; //用于保存输入的加数

    if ((keycode>=0x30) && (keycode<=0x39)){ //输入 0-9 的数字
        //整体十进制左移，新数字进入个位
        addend = (addend*10)+(keycode-0x30);
        ShowNumber(addend); //运算结果显示到数码管
        //向上键用作加号，执行加法或连加运算
    }else if (keycode == 0x26){
        result += addend;  //进行加法运算
        addend = 0;
        ShowNumber(result);  //运算结果显示到数码管
        //回车键，执行加法运算(实际效果与加号相同)
    }else if (keycode == 0x0D){
        result += addend;  //进行加法运算
        addend = 0;
        ShowNumber(result); //运算结果显示到数码管
    }else if (keycode == 0x1B){ //Esc 键，清零结果
        addend = 0;
        result = 0;
        ShowNumber(addend); //清零后的加数显示到数码管
    }
}
/* 按键驱动函数，检测按键动作，调度相应动作函数，需在主循环中调用 */
void KeyDriver(){
    unsigned char i, j;
    static unsigned char backup[4][4] = { //按键值备份，保存前一次的值
        {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
    };

    for (i=0; i<4; i++){ //循环检测 4*4 的矩阵按键
        for (j=0; j<4; j++){
            if (backup[i][j] != KeySta[i][j]){ //检测按键动作
                if (backup[i][j] != 0){ //按键按下时执行动作
                    KeyAction(KeyCodeMap[i][j]); //调用按键动作函数
                }
                backup[i][j] = KeySta[i][j];//刷新前一次的备份值
            }
        }
    }
}
/* 按键扫描函数，需在定时中断中调用，推荐调用间隔 1ms */
void KeyScan(){
    unsigned char i;
    //矩阵按键扫描输出索引
    static unsigned char keyout = 0;

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
    //每行 4 个按键，所以循环 4 次
    for (i=0; i<4; i++){
        //连续 4 次扫描值为 0，即 4*4ms 内都是按下状态时，可认为按键已稳定的按下
        if ((keybuf[keyout][i] & 0x0F) == 0x00){
            KeySta[keyout][i] = 0;
        //连续 4 次扫描值为 1，即 4*4ms 内都是弹起状态时，可认为按键已稳定的弹起
        }else if ((keybuf[keyout][i] & 0x0F) == 0x0F){
            KeySta[keyout][i] = 1;
        }
    }
    //执行下一次的扫描输出
    keyout++; //输出索引递增
    keyout = keyout & 0x03; //索引值加到 4 即归零
    //根据索引，释放当前输出引脚，拉低下次的输出引脚
    switch (keyout){
        case 0: KEY_OUT_4 = 1; KEY_OUT_1 = 0; break;
        case 1: KEY_OUT_1 = 1; KEY_OUT_2 = 0; break;
        case 2: KEY_OUT_2 = 1; KEY_OUT_3 = 0; break;
        case 3: KEY_OUT_3 = 1; KEY_OUT_4 = 0; break;
        default: break;
    }
}
/* 数码管动态扫描刷新函数，需在定时中断中调用 */
void LedScan(){
    static unsigned char i = 0; //动态扫描的索引
    P0 = 0xFF; //显示消隐

    switch (i){
        case 0: ADDR2=0; ADDR1=0; ADDR0=0; i++; P0=LedBuff[0]; break;
        case 1: ADDR2=0; ADDR1=0; ADDR0=1; i++; P0=LedBuff[1]; break;
        case 2: ADDR2=0; ADDR1=1; ADDR0=0; i++; P0=LedBuff[2]; break;
        case 3: ADDR2=0; ADDR1=1; ADDR0=1; i++; P0=LedBuff[3]; break;
        case 4: ADDR2=1; ADDR1=0; ADDR0=0; i++; P0=LedBuff[4]; break;
        case 5: ADDR2=1; ADDR1=0; ADDR0=1; i=0; P0=LedBuff[5]; break;
        default: break;
    }
}
/* T0 中断服务函数，用于数码管显示扫描与按键扫描 */
void InterruptTimer0() interrupt 1{
    TH0 = 0xFC; //重新加载初值
    TL0 = 0x67;
    LedScan(); //调用数码管显示扫描函数
    KeyScan(); //调用按键扫描函数
}
```