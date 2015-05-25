---
layout: post
title: 我也来做PSP游戏[1] - 编写Hello world
date: 2011-08-02 01:10:26.000000000 +08:00
categories:
- psp
tags:
- c
- psp
- 游戏开发
---

😳 声明:本站内的文章凡标题没标明 [转载] 或 [转] 字样的文章均为本人为研究计算机科学技术而亲笔，转载的亲们请帖上本站的连接和版权信息，谢谢。

下面进入正题，小弟08年买的小p，一直玩得不亦乐乎。不过后来还是被我雪藏了，因为小p魅力太大了，已经严重影响到我学习和生活，只能将它打入冷宫。现在出来工作了，有幸到了依家游戏公司工作，游戏，工作也，从前只会玩游戏，（实质是被游戏玩），现在我可以自己开发游戏，然后给游戏玩家玩（实质是玩玩家，哈哈，这句开玩笑的）。首先，无论是用何种语言编写应用程序还是游戏，我们都必须先从Hello world入手。所谓的hello world就是使用编程语言编写一个在输出设备上显示"Hello world!"字样的程序。这样的Hello world程序对学习编程有很重大的意义，他需要初学者掌握三个知识点。第一是学会配置编程环境，第二是学会编写最简单得包含输出操作的代码，第三是学会编译，第四学会运行（这个不用学了，忽略）。

一、工具准备
==========

### 1、devkitPro

这个是由一个外国大神制作的非官方psp开发工具，psp的官方版开发工具传闻要数万美元。

该软件的官方网址[http://devkitpro.org/](http://devkitpro.org/ "devkit pro")，下载链接暂时找不到。直接提供附件了， [devkitProUpdater-1.5.0.zip](/assets/psp/devkitProUpdater-1.5.0.zip "devkitProUpdater-1.5.0.zip")，下载后还需要在线安装，后面有介绍。

### 2、文本编辑器

随便找一个，仅用于代码编写，用系统自带的文本编辑器也可以（PS：\n在windows的记事本下不会显示为换行）

推荐使用notepad++，下载地址 [http://notepad-plus-plus.org/](http://notepad-plus-plus.org/)

### 3、计算机一台

这个- -，能编译程序的windows 32位计算机，使用其他系统的朋友请移步到google。

### 4、已XX的PSP 1000/2000/3000一台

其他机型，如PspGo的能不能运行，本人并不清楚。

二、配置环境
==========

### 1.下载并安装devkitPro

下载devkitPro的朋友，请按照下图进行安装，为了演示安装，我把我机器上的devkitPro都删了重装一遍😂。

![1-1](/assets/psp/1-1.jpg)

漫长的卸载中。。。

完毕了😊接下来打开devkitProUpdater 1.5.0，一路Next，直到出现下图为止。

![1-2](/assets/psp/1-2.jpg)

按照上图勾选，devkitARM里边的是GBA和NDS的开发工具，有兴趣的朋友可以勾上。
点击next进行下载

![1-3](/assets/psp/1-3.jpg)

### 2.添加环境变量

在环境变量中添加PSPSDK,值为你安装devkitPSP的位置.例如我的是

``E:\psp\devkitPro\devkitPSP``

再在Path变量尾端追加

``%PSPSDK%bin;%PSPSDK%psp\bin;``

![1-4](/assets/psp/1-4.jpg)

### 3.编译例子

安装以上操作后下载本章例子[psp-subject-01.zip](/assets/psp/psp-subject-01.zip "psp-subject-01.zip")

解压后新建一个文本文档，在里边输入如下代码

{% highlight c %}
make clean
make
cmd
{% endhighlight %}

然后保存为start.bat，双击start.bat得到如下界面表示编译成功

![1-5](/assets/psp/1-5.jpg)

### 4.运行程序

连接psp，在`psp\game\`目录下新建一个目录，名字随便起，将EBOOT.PBP放到新建文件夹内

![1-6](/assets/psp/1-6.jpg)

打开psp找到Subject-01运行

![1-7](/assets/psp/1-7.jpg)

程序运行如下界面

![1-8](/assets/psp/1-8.jpg)

恭喜你,你的第一个psp程序已成功编译并运行!

一些常见的编译错误:

1.使用c++编译时提示如下错误,重装devkitPro

{% highlight c %}
In function `_isatty_r':: undefined reference to `_isatty'
collect2: ld returned 1 exit status
{% endhighlight %}

2.使用c++提示无new操作符

在Makefile中加入`LIBS = -lstdc++`,将文件后缀改为cpp

3.make时出现如下错误,请将`devkitPsp\bin`和`devkitPsp\psp\bin`的完整路径添加到环境变量Path里

{% highlight c %}
make: psp-config: Command not found
Makefile:XX: /lib/build.mak: No such file or directory
{% endhighlight %}

4.头文件中类的定义结尾没有分号

{% highlight c %}
new types may not be defined in a return type
{% endhighlight %}

2015-01-29 更新
==============

devkitpro项目下载地址:

> [http://sourceforge.net/projects/devkitpro/](http://
sourceforge.net/projects/devkitpro/ "devkitpro")
