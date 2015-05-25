---
layout: post
title: 我也来做PSP游戏[4] - 获取按键状态
date: 2011-08-06 02:11:14.000000000 +08:00
categories:
- psp
tags:
- c
- psp
- 游戏开发
---

😝之前讲了屏幕如何显示颜色，跳过了获取按键状态一节，现在补上。 修改第三篇中`main`函数的代码

{% highlight c %}
int main()
{
	SceCtrlData pad;//控制器结构体,就是按键的状态
	pspDebugScreenInit();//初始化debug screen
	SetupCallbacks(); //设置回调线程
	
	sceCtrlSetSamplingCycle(0);//控制器状态更新周期,默认是0
	
	/* 控制器的模式 有数字（PSP_CTRL_MODE_DIGITAL）和类比（PSP_CTRL_MODE_ANALOG）两个 这里用的是类比的 */
	sceCtrlSetSamplingMode(PSP_CTRL_MODE_ANALOG);
	while(!done) {
		pspDebugScreenSetXY(0, 2);//设置光标位置
		sceCtrlReadBufferPositive(&pad, 1); //读取用户输入
		/* 类比摇杆的坐标*/
		pspDebugScreenPrintf("Analog X = %d", pad.Lx);
		pspDebugScreenPrintf("Analog Y = %d", pad.Ly);
		//调试屏幕输出信息,跟printf一样,必须在初始化debugscreen后使用
		//pspDebugScreenPrintf("Hello PSP");
		
		/* 其他按键的判断，按了哪个就输出相应的文字，用宏都表示出来了，看名字可以看出来*/
		if (pad.Buttons != 0) {
			if (pad.Buttons & PSP_CTRL_SQUARE) {
				printf("Square pressed");
			}
			if (pad.Buttons & PSP_CTRL_TRIANGLE) {
				printf("Triangle pressed");
			}
			if (pad.Buttons & PSP_CTRL_CIRCLE) {
				printf("Cicle pressed");
			}
			if (pad.Buttons & PSP_CTRL_CROSS) {
				printf("Cross pressed");
			}
			if (pad.Buttons & PSP_CTRL_UP) {
				printf("Up pressed");
			}
			if (pad.Buttons & PSP_CTRL_DOWN) {
				printf("Down pressed");
			}
			if (pad.Buttons & PSP_CTRL_LEFT) {
				printf("Left pressed");
			}
			if (pad.Buttons & PSP_CTRL_RIGHT) {
				printf("Right pressed");
			}
			
			if (pad.Buttons & PSP_CTRL_START) {
				printf("Start pressed");
			}
			if (pad.Buttons & PSP_CTRL_SELECT) {
				printf("Select pressed");
			}
			if (pad.Buttons & PSP_CTRL_LTRIGGER) {
				printf("L-trigger pressed");
			}
			if (pad.Buttons & PSP_CTRL_RTRIGGER) {
				printf("R-trigger pressed");
			}
		}
	}
	sceKernelExitGame(); //退出游戏
	return 0;
}
{% endhighlight %}

编译后放到PSP里后运行得到如下界面

![4-1](/assets/psp/4-1.jpg)

本节源码下载地址[psp-subject-04.zip](/assets/psp/psp-subject-04.zip)
