# D/A 输出

D/A 是和 A/D 刚好反方向的，一个 8 位的 D/A，从 0～255，代表了 0～2.55V 的话，那么我们用单片机给第三个字节发送 100，D/A 引脚就会输出一个 1V 的电压，发送 200 就输出一个 2V 的电压，很简单，我们用一个简单的程序实现出来，并且通过上、下按键可以增大或减小输出幅度值，每次增加或减小 0.1V。如果有万用表的话，可以直接测试一下板子上 AOUT 点的输出电压，观察它的变化。由于 PCF8591 的 DA 输出偏置误差最大是 50mv（由数据手册提供），所以我们用万用表测到的电压值和理论值之间的误差就应该在 50mV 以内。

/*****************************I2C.c 文件程序源代码*******************************/

（此处省略，可参考之前章节的代码）

/***************************keyboard.c 文件程序源代码****************************/

（此处省略，可参考之前章节的代码）

/*****************************main.c 文件程序源代码******************************/

```
#include <reg52.h>
unsigned char T0RH = 0; //T0 重载值的高字节
unsigned char T0RL = 0; //T0 重载值的低字节
void ConfigTimer0(unsigned int ms);
extern void KeyScan();
extern void KeyDriver();
extern void I2CStart();
extern void I2CStop();
extern bit I2CWrite(unsigned char dat);

void main(){
    EA = 1; //开总中断
    ConfigTimer0(1); //配置 T0 定时 1ms

    while (1){
        KeyDriver(); //调用按键驱动
    }
}
/* 设置 DAC 输出值，val-设定值 */
void SetDACOut(unsigned char val){
    I2CStart();
    if (!I2CWrite(0x48<<1)){ //寻址 PCF8591，如未应答，则停止操作并返回
        I2CStop();
        return;
    }
    I2CWrite(0x40); //写入控制字节
    I2CWrite(val); //写入 DA 值
    I2CStop();
}
/* 按键动作函数，根据键码执行相应的操作，keycode-按键键码 */
void KeyAction(unsigned char keycode){
    static unsigned char volt = 0; //输出电压值，隐含了一位十进制小数位
    if (keycode == 0x26){ //向上键，增加 0.1V 电压值
        if (volt < 25){
            volt++;
            SetDACOut(volt*255/25); //转换为 AD 输出值
        }
    }else if (keycode == 0x28){ //向下键，减小 0.1V 电压值
        if (volt > 0){
            volt--;
            SetDACOut(volt*255/25); //转换为 AD 输出值
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
/* T0 中断服务函数，执行按键扫描 */
void InterruptTimer0() interrupt 1{
    TH0 = T0RH; //重新加载重载值
    TL0 = T0RL;
    KeyScan(); //按键扫描
}
```