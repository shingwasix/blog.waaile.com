---
layout: post
title: 我也来做PSP游戏[2] - 浅析Hello world
date: 2011-08-03 01:50:31.000000000 +08:00
categories:
- psp
tags:
- c
- psp
- 游戏开发
author: Shingwa Six
---

上一篇写的Hello world,大家运行应该没问题吧!

那么今天这篇文章让我们一起来分析一下hello world程序里每一行代码的作用到底是什么，在看这篇文章之前，请确定自己已经对c或c++有所了解和认识。否则可以先到google补充一下c基础。

代码下载在第一篇文章里,如果没有看过第一篇的朋友请点这里 [《我也来做PSP游戏[1] - 编写Hello world》]({{ site.url }}/psp-game-develop-1 "我也来做PSP游戏 - 编写Hello world(1)")

好了,首先我们来看头文件

{% highlight c %}
#include <pspkernel.h>
#include <pspdebug.h>
{% endhighlight %}

从字面上可以大概了解他的用途了，第一个是psp的核心函数文件，包含线程操作等函数，第二个是用于debug即调试用的头文件，包含将调试信息输出到屏幕上的函数，跟c的`printf`函数差不多。

{% highlight c %}
PSP_MODULE_INFO("Subject-02", 0, 1, 1);
{% endhighlight %}

`PSP_MODULE_INFO`是一个宏函数，用于定义模块信息，第一个是模块名字，后面的0，1，1是版本号，做过开发的应该对这个版本号不陌生吧，第一位是程序大改动的版本号，第二个是修复bug或添加功能后的版本号，第三个是编译的版本号，我是这么理解的- -。

{% highlight c %}
int exit_callback(int arg1, int arg2, void *common)
{
	sceKernelExitGame();
	return 0;
}
{% endhighlight %}

这是一个用于响应退出按钮的函数，参数123和返回值暂时不知道有什么用，有知道的朋友麻烦告诉我一下。`exit_callback`函数名不是固定的，你可以将它改为你想要的名字，后面会用到该函数。`sceKernelExitGame`这个函数就是弹出退出提示对话框。

{% highlight c %}
int CallbackThread(SceSize args, void *argp)
{
	int cbid;
	
	cbid = sceKernelCreateCallback("Exit Callback", exit_callback, NULL);
	sceKernelRegisterExitCallback(cbid);
	
	sceKernelSleepThreadCB();
	
	return 0;
}
{% endhighlight %}

上面是一个回调线程函数，他的只要作用的是将之前的`exit_callback`函数注册为退出按键事件回调函数。

{% highlight c %}
int SetupCallbacks(void)
{
	int thid = 0;
	thid = sceKernelCreateThread("update_thread", CallbackThread, 0x11, 0xFA0, 0, 0);
	if(thid &gt;= 0)
	{
		sceKernelStartThread(thid, 0, 0);
	}
	return thid;
}
{% endhighlight %}

上面的函数作用是根据回调线程函数创建一个新的线程，然后创建成功系统则启用这个线程，返回线程id。

{% highlight c %}
int main()
{
	pspDebugScreenInit();
	SetupCallbacks();
	pspDebugScreenPrintf("Hello world");
	sceKernelSleepThread();
	return 0;
}
{% endhighlight %}

main函数就c的主程序入口了，所有的代码执行都应该从这里开始，`pspDebugScreenInit`是初始化调试屏幕，`pspDebugScreenPrintf`是向屏幕输出调试信息的函数，跟c语言的`pritf`用法一样，支持字符串格式化，使用这个函数前必须初始化调试屏幕。`sceKernelSleepThread`是让当前主线程睡眠，不然程序会直接执行`return 0`，然后退出。

还有Makefile里的

{% highlight c %}
PSP_EBOOT_TITLE = Sunject-01
{% endhighlight %}

是用于定义程序在psp上显示的标题的.

今天的hello world就解释到这里，小弟不才，如有错误或遗漏之处，欢迎高手更正指点。

含注释的hello world下载[psp-subject-02.zip](/assets/psp/psp-subject-02.zip "psp-subject-02.zip")

Good night!😌
