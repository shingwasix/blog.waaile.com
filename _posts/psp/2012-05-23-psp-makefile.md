---
layout: post
title: '[转]PSP Makefile写法'
date: 2012-05-23 02:15:27.000000000 +08:00
categories:
- psp
tags:
- c
- makefile
- psp
- 游戏开发
---

> 转自 [http://www.dcw-team.com/blog/archives/260](http://www.dcw-team.com/blog/archives/260)

{% highlight makefile %}
#定义编译的elf/prx的文件名，不能是中文，也不能含有特殊字符及空格。
TARGET = main

#需要编译的obj文件
OBJS = common.o crt0.o main.o

#包含引用的头文件的文件夹
INCDIR = include

#-O (-O1), -O0, -O2, -O3, -Os 依照后面数字的大小，针对效能最佳化的程度也不同 (稳定度也可能递减)。其中 -Os 是个比较特殊的等级，针对原始码大小最佳化。 可使用 -Os，降低程序加载的时间。
CFLAGS = -Wall -Os -G0
CXXFLAGS = $(CFLAGS) -fno-exceptions -fno-rtti
ASFLAGS = $(CFLAGS) -c

#lib文件的所在
LIBDIR = lib
LDFLAGS += -nostdlib

#定义使用的lib
LIBS = -lz

#从3.xx系统开始必须编译成PRX
BUILD_PRX = 1

#定义系统版本？
PSP_FW_VERSION =500

#最终生成的文件
EXTRA_TARGETS = EBOOT.PBP

#*1和*2选其一就行，当要使用中文的程序显示名称时，可以用*2的方法，先自己制作一份SFO文件。
#在PSP上显示的程序名，可以有空格，但不能中文（*1）
PSP_EBOOT_TITLE = TITLE
#定义程序信息文件（*2）
#PSP_EBOOT_SFO = PARAM.SFO

#定义显示的程序图标
PSP_EBOOT_ICON = ICON0.png

#定义显示的程序动画
PSP_EBOOT_ICON1 = ICON1.pmf

#定义右下角的介绍图
PSP_EBOOT_UNKPNG = PIC0.png

#定义程序背景图
PSP_EBOOT_PIC1 = PIC1.png

#定义程序背景声音
PSP_EBOOT_SND0 = SND0.at3

#定义打包到PBP内的数据文件
PSP_EBOOT_PSAR = files.zip

#PSPSDK的路径
PSPSDK = $(shell psp-config –pspsdk-path)
include $(PSPSDK)/lib/build.mak
{% endhighlight %}
