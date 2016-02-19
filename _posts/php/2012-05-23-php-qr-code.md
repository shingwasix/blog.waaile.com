---
layout: post
title: PHP网站添加QR-Code二维码生成库
date: 2012-05-23 02:36:36.000000000 +08:00
categories:
- php
tags:
- php
- PHP QR code
- 开源项目
status: publish
type: post
published: true
author: Shingwa Six
---

PHP QR Code是一个开源的PHP二维码开源类库，基于libqrencode C库，并提供API代码创建QR条码图像，支持PNG、JPEG格式。功能强大，使用起来也非常简单。

demo代码如下：

{% highlight php %}
<?php
include "./phpqrcode/phpqrcode.php";
$value = "{{ site.url }}";
$errorCorrectionLevel = "L";
$matrixPointSize = "4";
QRcode::png($value, false, $errorCorrectionLevel, $matrixPointSize);
exit;
{% endhighlight %}

PHP QR code项目源码下载地址 [http://sourceforge.net/projects/phpqrcode/](http://sourceforge.net/projects/phpqrcode/)

libqrencode项目源码下载地址 [http://fukuchi.org/works/qrencode/](http://fukuchi.org/works/qrencode/)

将PHP QR code库放在站点个目录后,

打开 [http://127.0.0.1/phpqrcode/](http://127.0.0.1/phpqrcode/) 可以看到如下界面

[![php-qrcode](/assets/php/php-qrcode.png)](/assets/php/php-qrcode.png)

如果运行时提示 `Call to undefined function imageCreate()` 错误,请按照下述方法进行配置

在 `php.ini` 中去掉 `extension=php_gd2.dll` 前边的 `;`
将 `php.ini` 拷贝到 `C:\\windows` 文件夹里
然后将php目录中的ext下的 `php_gd2.dll` 拷入 `C:\\windows\system32`
