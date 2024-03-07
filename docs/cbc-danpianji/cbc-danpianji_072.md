# 实用的 28BYJ-48 步进电机控制程序

上面我们虽然完成了用中断控制电机转动的程序，但实际上这个程序还是没多少实用价值的，我们不能每次想让它转动的时候都上下电啊，是吧。还有就是它不但能正转还得能反转啊，也就是说不但能转过去，还得能转回来呀。好吧，我们就来做一个实例程序吧，结合第八章的按键程序，我们设计这样一个功能程序：按数字键 1～9，控制电机转过 1～9 圈；配合上下键改变转动方向，按向上键后正向转 1～9 圈，向下键则反向转 1～9 圈；左键固定正转 90 度，右键固定反转 90；Esc 键终止转动。通过这个程序，我们也可以进一步体会到如何用按键来控制程序完成复杂的功能，以及控制和执行模块之间如何协调工作，而你的编程水平也可以在这样的实践练习中得到锻炼和提升。

```
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
    { 0x31, 0x32, 0x33, 0x26 }, //数字键 1、数字键 2、数字键 3、向上键
    { 0x34, 0x35, 0x36, 0x25 }, //数字键 4、数字键 5、数字键 6、向左键
    { 0x37, 0x38, 0x39, 0x28 }, //数字键 7、数字键 8、数字键 9、向下键
    { 0x30, 0x1B, 0x0D, 0x27 } //数字键 0、ESC 键、 回车键、 向右键
};
unsigned char KeySta[4][4] = { //全部矩阵按键的当前状态
    {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}, {1, 1, 1, 1}
};
signed long beats = 0; //电机转动节拍总数
void KeyDriver();

void main(){
    EA = 1; //使能总中断
    TMOD = 0x01; //设置 T0 为模式 1
    TH0 = 0xFC; //为 T0 赋初值 0xFC67，定时 1ms
    TL0 = 0x67;
    ET0 = 1; //使能 T0 中断
    TR0 = 1; //启动 T0

    while (1){
        KeyDriver(); //调用按键驱动函数
    }
}
/* 步进电机启动函数，angle-需转过的角度 */
void StartMotor(signed long angle){
    //在计算前关闭中断，完成后再打开，以避免中断打断计算过程而造成错误
    EA = 0;
    beats = (angle * 4076) / 360; //实测为 4076 拍转动一圈
    EA = 1;
}
/* 步进电机停止函数 */
void StopMotor(){
    EA = 0;
    beats = 0;
    EA = 1;
}
/* 按键动作函数，根据键码执行相应的操作，keycode-按键键码 */
void KeyAction(unsigned char keycode){
    static bit dirMotor = 0; //电机转动方向
    //控制电机转动 1-9 圈
    if ((keycode>=0x30) && (keycode<=0x39)){
        if (dirMotor == 0){
            StartMotor(360*(keycode-0x30));
        }else{
            StartMotor(-360*(keycode-0x30));
        }
    }else if (keycode == 0x26){ //向上键，控制转动方向为正转
        dirMotor = 0;
    }else if (keycode == 0x28){ //向下键，控制转动方向为反转
        dirMotor = 1;
    }else if (keycode == 0x25){ //向左键，固定正转 90 度
        StartMotor(90);
    }else if (keycode == 0x27){ //向右键，固定反转 90 度
        StartMotor(-90);
    }else if (keycode == 0x1B){ //Esc 键，停止转动
        StopMotor();
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
/* 电机转动控制函数 */
void TurnMotor(){
    unsigned char tmp; //临时变量
    static unsigned char index = 0; //节拍输出索引
    unsigned char code BeatCode[8] = { //步进电机节拍对应的 IO 控制代码
        0xE, 0xC, 0xD, 0x9, 0xB, 0x3, 0x7, 0x6
    };

    if (beats != 0){ //节拍数不为 0 则产生一个驱动节拍
        if (beats > 0){ //节拍数大于 0 时正转
            index++; //正转时节拍输出索引递增
            index = index & 0x07; //用&操作实现到 8 归零
            beats--; //正转时节拍计数递减
            }else{ //节拍数小于 0 时反转
            index--; //反转时节拍输出索引递减
            index = index & 0x07; //用&操作同样可以实现到-1 时归 7
            beats++; //反转时节拍计数递增
        }
        tmp = P1; //用 tmp 把 P1 口当前值暂存
        tmp = tmp & 0xF0; //用&操作清零低 4 位
        tmp = tmp | BeatCode[index]; //用|操作把节拍代码写到低 4 位
        P1 = tmp; //把低 4 位的节拍代码和高 4 位的原值送回 P1
    }else{ //节拍数为 0 则关闭电机所有的相
        P1 = P1 | 0x0F;
    }
}
/* T0 中断服务函数，用于按键扫描与电机转动控制 */
void InterruptTimer0() interrupt 1{
    static bit div = 0;
    TH0 = 0xFC; //重新加载初值
    TL0 = 0x67;
    KeyScan(); //执行按键扫描
    //用一个静态 bit 变量实现二分频，即 2ms 定时，用于控制电机
    div = ~div;
    if (div == 1){
        TurnMotor();
    }
}
```

这个程序是第八章和本章知识的一个综合——用按键控制步进电机转动。程序中有这么几点值得注意，我们分述如下：

*   针对电机要完成正转和反转两个不同的操作，我们并没有使用正转启动函数和反转启动函数这么两个函数来完成，也没有在启动函数定义的时候增加一个形式参数来指明其方向。我们这里的启动函数 void StartMotor(signed long angle)与单向正转时的启动函数唯一的区别就是把形式参数 angle 的类型从 unsigned long 改为了 signed long，我们用有符号数固有的正负特性来区分正转与反转，正数表示正转 angle 度，负数就表示反转 angle 度，这样处理是不是很简洁又很明了呢？而你对有符号数和无符号数的区别用法是不是也更有体会了？
*   针对终止电机转动的操作，我们定义了一个单独的 StopMotor 函数来完成，尽管这个函数非常简单，尽管它也只在 Esc 按键分支内被调用了，但我们仍然把它单独提出来作为了一个函数。而这种做法就是基于这样一条编程原则：尽可能用单独的函数来完成硬件的某种操作，当一个硬件包含多个操作时，把这些操作函数组织在一起，形成一个对上层的统一接口。这样的层次化处理，会使得整个程序条理清晰，既有利于程序的调试维护，又有利于功能的扩充。
*   中断函数中要处理按键扫描和电机驱动两件事情，而为了避免中断函数过于复杂，我们就又分出了按键扫描和电机驱动两个函数（这也同样符合上述 2 的编程原则），而中断函数的逻辑就变得简洁而清晰了。这里还有个矛盾，就是按键扫描我们选择的定时时间是 1ms，而本章之前的实例中电机节拍持续时间都是 2ms；很显然，用 1ms 的定时可以定出 2ms 的间隔，而用 2ms 的定时却得不到准确的 1ms 间隔；所以我们的做法就是，定时器依然定时 1ms，然后用一个 bit 变量做标志，每 1ms 改变一次它的值，而我们只选择值为 1 的时候执行一次动作，这样就是 2ms 的间隔了；如果我要 3ms、4ms„„呢，把 bit 改为 char 或 int 型，然后对它们递增，判断到哪个值该归零，就可以了。这就是在硬件定时器的基础上实现准确的软件定时，其实类似的操作我们在讲数码管的时候也用过了，回想一下吧。