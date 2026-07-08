# stm32_learning_joruney
# 日期 2026-7-8
Q1.怎么理解：“端口配置高寄存器”和“端口输出数据寄存器”
：端口（Port）：指一组引脚。比如 GPIOA 就是一个端口，它里面包含了 16 个具体的引脚。端口配置高寄存器 (GPIOx_CRH)：大白话理解：“用来设置第 8 到 15 号引脚工作模式的控制面板”。
 Q2：“一组引脚”中的“一组”指的是什么？
：在单片机里，“一组”指的是 端口（Port）。
STM32 芯片上的引脚很多，如果一个个单独命名（比如 1号、2号、3号……一直到 100号），用起来会极其混乱。所以设计师把它们按 16 个引脚划分为一组，并用英文字母 A、B、C、D 等来命名这些“组”。
A 组 (GPIOA)：包含 PA0, PA1, PA2 ... 一直到 PA15（共16个引脚）。
B 组 (GPIOB)：包含 PB0, PB1, PB2 ... 一直到 PB15（共16个引脚）。
C 组 (GPIOC)：包含 PC0, PC1, PC2 ... 一直到 PC15。
(注意：您手中的这块蓝色板子上，C 组只引出了 PC13、PC14、PC15 这几个引脚，其中 PC13 旁边还接了一个板载的 LED 灯，就是您照片中 Y1 晶振右侧标着 “PC13” 的位置。)
Q3：“推挽输出”、“开漏输出”与“速度”如何理解？
： 推挽输出（Push-Pull）—— 最常用的普通输出
工作方式：引脚内部有两个“强力开关”，一个接 VCC（3.3V），一个接 GND（0V）。
当你输出 1 时，它会主动、强力地把电流**推（Push）**出去，输出 3.3V；
当你输出 0 时，它会强力地把电流**挽（Pull）**进来，拉低到 0V。
应用场景：最常见的控制，比如直接点亮 LED、给别的芯片发送普通信号。
② 开漏输出（Open-Drain）—— “只拉低，不推高”的输出
工作方式：引脚内部只有一个接 GND（0V）的开关。
当你输出 0 时，它会把引脚拉低到 0V；
当你输出 1 时，它内部的开关直接断开（悬空，相当于引脚断线了），它自己无法主动输出 3.3V 的高电平。如果想让它输出高电平，必须在外部接一个电阻拉到电源上（称为上拉电阻）。
应用场景：主要用于 I2C 这种需要多个设备共用一根线的通信总线，或者需要用 3.3V 芯片去控制 5V 设备的场景。
Q4：32位装下64位数据，具体在代码/硬件上如何处理？
由于配置 16 个引脚需要 64 位，而一个寄存器只有 32 位，所以芯片里做成了两个寄存器：CRL（低寄存器，管引脚 07）和 CRH（高寄存器，管引脚 815）。
在处理时，具体是这样映射的：每个引脚需要 4 位（Bit）来配置。
如果你要配置的是 Pin 0 ~ 7 之间的引脚，硬件会去读写 CRL。
如果你要配置的是 Pin 8 ~ 15 之间的引脚，硬件会去读写 CRH。
代码：
#include "stm32f10x.h"                  // Device header

int main(void)
{
	RCC->APB2ENR = 0x10;
	GPIOC->CRH = 0x300000;  //端口配置高寄存器
	GPIOC->ODR = 0x2000;  //端口输出数据寄存器
	//GPIOC->ODR = 0x0000;  //端口输出数据寄存器
	while(1)
	{
	
	}

}
使用库函数电灯：
#include "stm32f10x.h"                  // Device header

int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC,ENABLE);
	//配置端口模式
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_13;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOC,&GPIO_InitStructure);
	//设置端口的高低电平
	GPIO_SetBits(GPIOC,GPIO_Pin_13); //高电平
	//GPIO_ResetBits(GPIOC,GPIO_Pin_13); //低电平
	while(1)
	{
	
	}

}
-------------------达成成就：电灯大师-------------------
