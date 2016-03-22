---
layout: post
title: 解决Mac OS X 10.9使用Time Machine备份缓慢的问题
date: 2013-12-27 14:04:11.000000000 +08:00
categories:
- mac
- note
tags:
- mac
- timemachine
author: Shingwa Six
---

上两个月升级了Mac OSX 10.9后一直没有备份过，前几天用移动硬盘备份的时候，发现备份速度极其缓慢。

我使用的是1T的2.5英寸移动硬盘，起初以为是硬盘的问题，但测试过读写文件完全没问题，速度一点也不慢。

本人要备份的硬盘大小为70G，基本上是挂了几晚机才能进行一次完整的备份。

再次进行增量备份5G东西的时候，Time Machine的进度条显示备份了几M之后就死活不肯动了，挂个几小时都不动一下！

当时我就开始怀疑是不是我的硬盘里有问题文件导致无法备份，于是很无奈地整个mac硬盘格式化了，再重新安装一次系统。。。

当我重装完后，以为事情就这么完了，可惜的是杯具又在出现- -|||

无奈的我只能慢慢等了

但就在我等待的过程中，我无意点开了Spotlight，然后发现他在提示正在为我移动硬盘上的其他分区生成索引。

![timemachine_slow_1](/assets/osx/timemachine_slow_1.jpg)

然后我就怀疑是Spotlight这个可恶的东西导致备份缓慢，于是我把其中非备份的分区都卸载掉了，Time Machine的速度立马又上来了！

![timemachine_slow_3](/assets/osx/timemachine_slow_3.jpg)

原来罪魁祸首就是Spotlight，10.9下插入移动硬盘他就会自动创建索引(貌似之前10.7没有出现过这种事) ,并在分区的根目录下生成`.Spotlight-V100`这个文件夹

![timemachine_slow_4](/assets/osx/timemachine_slow_4.jpg)

&nbsp;

对于文件数量比较多的分区，生成的索引文件也是比较大的（以下这个索引文件还没完全生成，完全生成可能会超过1G）

![timemachine_slow_2](/assets/osx/timemachine_slow_2.jpg)

要想Time Machine备份的时候速度够快，除了可以在备份的时候卸载掉其他无用分区之外，还可以使用以下方法禁止某个Spotlight对某个分区进行备份

![timemachine_slow_5](/assets/osx/timemachine_slow_5.jpg)

使用终端进入到分区根目录

利用命令`touch .metadata_never_index`在根目录下生成`.metadata_never_index`文件，或者使用`vi .metadata_never_index`命令进行生成也行

如果提示`Permission denied`，命令前面加上sudo执行后输入密码即可

重新装载硬盘分区，Spotlight就会乖乖的不对分区做任何处理了
