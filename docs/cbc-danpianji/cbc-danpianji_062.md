# 单片机矩阵按键的扫描

我们讲独立按键扫描的时候，大家已经简单认识了矩阵按键是什么样子了。矩阵按键相当于 4 组每组各 4 个独立按键，一共是 16 个按键。那我们如何区分这些按键呢？想一下我们生活所在的地球，要想确定我们所在的位置，就要借助经纬线，而矩阵按键就是通过行线和列线来确定哪个按键被按下的。那么在程序中我们又如何进行这项操作呢？

前边讲过，按键按下通常都会保持 100ms 以上，如果在按键扫描中断中，我们每次让矩阵按键的一个 KeyOut 输出低电平，其它三个输出高电平，判断当前所有 KeyIn 的状态，下次中断时再让下一个 KeyOut 输出低电平，其它三个输出高电平，再次判断所有 KeyIn，通过快速的中断不停的循环进行判断，就可以最终确定哪个按键按下了，这个原理是不是跟数码管动态扫描有点类似？数码管我们在动态赋值，而按键这里我们在动态读取状态。至于扫描间隔时间和消抖时间，因为现在有 4 个 KeyOut 输出，要中断 4 次才能完成一次全部按键的扫描，显然再采用 2ms 中断判断 8 次扫描值的方式时间就太长了（2*4*8=64ms），那么我们就改用 1ms 中断判断 4 次采样值，这样消抖时间还是 16ms(1*4*4)。下面就用程序实现出来，程序循环扫描板子上的 K1～K16 这 16 个矩阵按键，分离出按键动作并在按键按下时把当前按键的编号显示在一位数码管上（用 0~F 表示，显示值=按键编号-1）。

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
unsigned char KeySta[4][4] = { //全部矩阵按键的当前状态
    {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
};

void main(){
    unsigned char i, j;
    unsigned char backup[4][4] = { //按键值备份，保存前一次的值
        {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
    };

    EA = 1;  //使能总中断
    ENLED = 0;  //选择数码管 DS1 进行显示
    ADDR3 = 1;
    ADDR2 = 0;
    ADDR1 = 0;
    ADDR0 = 0;
    TMOD = 0x01;  //设置 T0 为模式 1
    TH0 = 0xFC;  //为 T0 赋初值 0xFC67，定时 1ms
    TL0 = 0x67;
    ET0 = 1;  //使能 T0 中断
    TR0 = 1;  //启动 T0
    P0 = LedChar[0];  //默认显示 0

    while (1){
        for (i=0; i<4; i++){ //循环检测 4*4 的矩阵按键
            for (j=0; j<4; j++){
                if (backup[i][j] != KeySta[i][j]){ //检测按键动作
                    if (backup[i][j] != 0){ //按键按下时执行动作
                        P0 = LedChar[i*4+j];  //将编号显示到数码管
                    }
                    backup[i][j] = KeySta[i][j]; //更新前一次的备份值
                }
            }
        }
    }
}
/* T0 中断服务函数，扫描矩阵按键状态并消抖 */
void InterruptTimer0() interrupt 1{
    unsigned char i;
    static unsigned char keyout = 0; //矩阵按键扫描输出索引
    static unsigned char keybuf[4][4] = { //矩阵按键扫描缓冲区
        {0xFF, 0xFF, 0xFF, 0xFF}, {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF}, {0xFF, 0xFF, 0xFF, 0xFF}
    };

    TH0 = 0xFC; //重新加载初值
    TL0 = 0x67;
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
            //连续 4 次扫描值为 1，即 4*4ms 内都是弹起状态时，可认为按键已稳定的弹起
        }else if ((keybuf[keyout][i] & 0x0F) == 0x0F){
            KeySta[keyout][i] = 1;
        }
    }
    //执行下一次的扫描输出
    keyout++;  //输出索引递增
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
```

这个程序完成了矩阵按键的扫描、消抖、动作分离的全部内容，希望大家认真研究一下，彻底掌握矩阵按键的原理和应用方法。在程序中还有两点值得说明一下。

首先，可能你已经发现了，中断函数中扫描 KeyIn 输入和切换 KeyOut 输出的顺序与前面提到的顺序不同，程序中我首先对所有的 KeyIn 输入做了扫描、消抖，然后才切换到了下一次的 KeyOut 输出，也就是说我们中断每次扫描的实际是上一次输出选择的那行按键，这是为什么呢？因为任何信号从输出到稳定都需要一个时间，有时它足够快而有时却不够快，这取决于具体的电路设计，我们这里的输入输出顺序的颠倒就是为了让输出信号有足够的时间（一次中断间隔）来稳定，并有足够的时间来完成它对输入的影响，当你的按键电路中还有硬件电容消抖时，这样处理就是绝对必要的了，虽然这样使得程序理解起来有点绕，但它的适应性是最好的，换个说法就是，这段程序足够“健壮”，足以应对各种恶劣情况。

其次，是一点小小的编程技巧。注意看 keyout = keyout & 0x03;这一行，在这里我是要让 keyout 在 0～3 之间变化，加到 4 就自动归零，按照常规你可以用前面讲过的 if 语句轻松实现，但是你现在看一下这样程序是不是同样可以做到这一点呢？因为 0、1、2、3 这四个数值正好占用 2 个二进制的位，所以我们把一个字节的高 6 位一直清零的话，这个字节的值自然就是一种到 4 归零的效果了。看一下，这样一句代码比 if 语句要更为简洁吧，而效果完全一样。