---
layout: post
title: 树莓派Zero USB无头启动
date: 2020-04-10 01:14:12 +08:00
categories:
- raspberrypi
tags: [raspberrypi]
status: publish
type: post
published: true
author: Shingwa Six
---

![headless_00](/assets/raspberrypi/headless_00.jpg)

# 简述

所谓无头启动是指不依赖显示器、键盘、鼠标这些输入输出设备进行设备启动，树莓派官方称之为 headless 模式

官方的设置无头模式说明链接如下：
[https://www.raspberrypi.org/documentation/configuration/wireless/headless.md](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md)

官方说明的无头模式需要依赖 Wi-Fi 功能，本文主要讲述的是仅使用 USB 接口进行连接操作的启动方式

# 硬件

+ 树莓派 Zero / Zero W / Zero WH （其他型号的树莓派貌似也是可以的，但具体怎么连线本人不太清楚）
+ Micro SD（TF） 读卡器
+ Micro SD（TF） 卡
+ Micro USB 2.0 数据线
+ 电脑（笔者使用的是 Macbook，本文中所有的操作均在 macOS 系统下完成）

本文中使用的树莓派型号为 树莓派 Zero WH，到目前为止树莓派 Zero 总共出了三个不同的版本，分别为：

+ 树莓派 Zero （无Wi-Fi，无蓝牙）
+ 树莓派 Zero W（带2.4G Wi-Fi，带蓝牙4.0）
+ 树莓派 Zero WH（带2.4G Wi-Fi，带蓝牙4.0，以及焊接了彩色排针）

# 软件

+ [Raspbian](https://www.raspberrypi.org/downloads/raspbian/) - 树莓派操作系统，本文中使用的系统是 2020-02-13 的版本
+ [balenaEtcher](https://www.balena.io/etcher/) - 用于刻录系统到 TF 卡，本文中使用的软件版本为 1.5.63

树莓派下载太慢的话，可以考虑复制 `Download ZIP` 按钮中的链接然后使用 [百度网盘](https://pan.baidu.com/) 离线下载。虽然百度网盘下载文件也限速，但还是比几K一秒的速度强得多。

# 刻录系统

把 TF 卡插到读卡器中并插入到电脑 USB 口中

![headless_01 @2x](/assets/raspberrypi/headless_01.jpg)

打开下载并安装好的 balenaEtcher，选择系统镜像（树莓派系统镜像）和需要刻录的磁盘（TF卡）

![headless_02 @2x](/assets/raspberrypi/headless_02.png)

点击 Flash! 按钮后根据提示输入当前用户密码

![headless_03 @2x](/assets/raspberrypi/headless_03.png)

等待刻录完成。笔者刻录的是 lite 版系统镜像，不包含桌面程序和其他推荐程序。刻录大小只有1.85G，所以刻录得比较快，大概也就3分钟

![headless_04 @2x](/assets/raspberrypi/headless_04.png)

当前 balenaEtcher 提示 Flash Complete! 的时候，即为刻录完成，此时可以关闭 balenaEtcher

![headless_05 @2x](/assets/raspberrypi/headless_05.png)

刻录完成后，你会发现 Finder（访达）中看不到 TF 卡的任何分区，这是因为 TF 卡的储存分区没有被系统装载。此时可以通过拔出再插入读卡器来装载 TF 卡分区，或者使用 macOS 实用工具中的「磁盘工具」软件操作装载 TF 卡分区

![headless_06 @2x](/assets/raspberrypi/headless_06.png)

装载成功后，Finder 中的侧栏即可看到一个名为 `boot` 的分区

![headless_07 @2x](/assets/raspberrypi/headless_07.png)

`boot` 分区里边是一堆树莓派启动所需的配置文件

![headless_08 @2x](/assets/raspberrypi/headless_08.png)

# 设置开启 SSH 服务

要设置树莓派默认开启 ssh 服务很简单，只需要在 `boot` 分区下创建一个空白的 ssh 文件即可。

macOS 下可以使用「终端」执行如下指令完成：

```
touch /Volumes/boot/ssh
```

# 设置开启 USB 网口功能

使用「文本编辑」应用打开并编辑 `boot` 分区下的 config.txt，在文件最后加入以下内容保存

```
# USB Ethernet Gadget
dtoverlay=dwc2
```

保存时若弹出以下提示点击 “好” 即可

![headless_09 @2x](/assets/raspberrypi/headless_09.jpg)

在 `boot` 中的 cmdline.txt 行未追加以下内容

```
modules-load=dwc2,g_ether
```

需要注意的是，cmdline.txt 这个文件下的内容只有一行，追加的内容与追加之前内容需要使用空格隔开并且保持在同一行，追加后的内容大概是这样子的：

```
console=serial0,115200 console=tty1 root=PARTUUID=738a4d67-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet init=/usr/lib/raspi-config/init_resize.sh modules-load=dwc2,g_ether
```

至此，树莓派已经具备无头启动的能力了。如果你使用的树莓派带有 Wi-Fi 模块，并且你希望它启动时自动连接到某个 Wi-Fi，那么你可以继续参照「配置无线网络」进行操作。否则，请跳到「启动系统」一节

# 配置无线网络

在 `boot` 分区下创建名为 wpa_supplicant.conf 的配置文件，按照如下格式在配置文件中添加内容并保存

```
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="WIFI_0"
    psk="password"
    key_mgmt=WPA-PSK
    scan_ssid=1
    priority=1
}

network={
    ssid="WIFI_1"
    psk="password"
    key_mgmt=WPA-PSK
    scan_ssid=1
}
```

> ⚠️此处需要注意的是，树莓派 Zero 仅支持连接 2.4G Wi-Fi 网络

# 启动系统

推出 TF 卡并插入到树莓派的卡槽中，使用 Micro USB 2.0 数据线连接树莓派和电脑。

说明：

> 树莓派 Zero 中有两个 USB 接口，其中一个写着「PWR IN」的 USB 接口是专门用于供电的，无法传输数据；而写着「USB」的 USB 接口，则既可以供电，也可以传输数据。此处我们的 USB 数据线需要连接中间这个带有「USB」标示的接口

![headless_10 @2x](/assets/raspberrypi/headless_10.jpg)

连接数据线后，稍等片刻树莓派即启动完成。

> 树莓派板子上的绿色指示灯常亮不闪烁时即为启动完成

打开网络偏好设置，可以看到多了一个名为「RNDIS/Ethernet Gadget」的网络，这个就是通过 USB 连接到树莓派的网络。

> ⚠️注意：此处的 IP地址 为树莓派分配给连接端的地址，即电脑本机的 IP地址，故无法通过该地址进行 ssh 连接

![headless_11 @2x](/assets/raspberrypi/headless_11.png)

# 连接 SSH

通过 USB 与树莓派接入同一个网络后，即可通过本地域名 `raspberrypi.local` 与树莓派进行 ssh 连接了

树莓派系统默认的用户名为：pi 密码为：raspberry

使用「终端」进行 ssh 连接的指令如下：

```
ssh pi@raspberrypi.local
```

Done. ☕️




