---
layout: post
title: Let's Encrypt 使用免费的SSL证书来加密
date: 2016-04-05 22:49 +08:00
categories:
- note
- web
- https
tags:
- wordpress
- 摘要长度
status: publish
type: post
published: true
author: Shingwa Six
---

[Let's Encrypt] 是一个全新的SSL证书颁发组织，它旨在向广大互联网用户提供免费的、自动化的和开放的证书认证服务。其证书认证服务目前处于公测阶段。

![letsencrypt-00][letsencrypt-00]

由于其属于公益组织，所以也得到不了少互联网企业组织的支持赞助，其中不乏[Mozilla]、[Google Chrome]、[Cisco]、[Facebook]这些大企业组织的支持。

![letsencrypt-01][letsencrypt-01]

根据该组织的官方推特 [@letsencrypt] 显示，他们从今年2月到3月一个月间生成的证书有超过50万个，截止3月8日共有超过100万个证书生成并用于超过240万个域名上。

![letsencrypt-02][letsencrypt-02]

该机构颁发的证书不但免费，而且兼容性还非常好。根据 [https://www.ssllabs.com](https://www.ssllabs.com) 的测试结果来看， [Let's Encrypt] 颁发的证书几乎兼容所有主流操作系统和浏览器，数十个模拟握手结果中只有万恶的 [IE 6/XP] 没有握手成功。

![letsencrypt-03][letsencrypt-03]

### 部署

下面我将以 [CentOS 7.2] 以及 [Nginx] 为基础向大家说明如何使用官方提供的工具来生成SSL证书，并让服务器支持 [HTTPS] 请求。

#### 1. 克隆letsencrypt项目

首先，我们需要登入到服务器内，然后克隆 [Let's Encrypt] 存放在 [GitHub] 上的证书自助申请项目，具体命令如下：

{% highlight sh %}
# 如果没有安装Git，需要先安装
yum install git
# 存放在哪个位置具体取决于你自己，以下示例中我将会把项目克隆到/var/letsencrypt下
cd /var
git clone https://github.com/letsencrypt/letsencrypt
{% endhighlight %}

#### 2. 申请证书

letsencrypt项目下的 [letsencrypt-auto] 脚本包含了所有我们需要用到的功能指令，包括申请证书、撤销证书以及重申证书。

[Let's Encrypt] 目前并不支持泛域名证书申请，但支持多域名证书申请，意思是你可以申请一个证书用于服务多个不同的域名。

在申请证书之前，我们需要先将需要申请证书的域名全部指向到当前主机上，这将在后面的域名所有权认证时用到。

> 有部分网友分享指出使用国内阿里的DNS解析服务有可能导致letsencrypt的服务器解析域名失败，若想保证域名能成功解析的话可以使用国外CloudFlare的免费域名解析服务

{% highlight sh %}
# 如果没有安装Python 2.7或Virtualenv，需要先安装，Virtualenv需要安装完Python 2.7后才可安装
yum install python
pip installl virtualenv
{% endhighlight %}

在确保所需的软件已安装后，继续根据以下操作进行证书申请。[letsencrypt] 一共提供了三种方式验证域名所有权并申请证书，分别是“\-\-apache”、“\-\-standalone”、“\-\-webroot”。“\-\-apache”模式利用Apache插件进行验证，适合使用Apache Httpd作为 [Web Server] 的用户使用；“\-\-standalone”模式是通过创建一个临时HTTP服务进行域名所有权验证，对于已安装 [Web Server] 的服务器需要先停止 [Web Server] 等于申请完毕后启用，由于证书续订依然需要临时停止 [Web Server] 以验证域名所有权，故不推荐使用该模式；“\-\-webroot”模式则是通过webroot插件向特定目录下写入文件，然后通过指定的URL进行访问验证，该模式适用于任何已安装了 [Web Server] 的服务器使用，我们将使用该模式进行申请。

首先，将需要申请证书的网站配置文件（位于`/etc/nginx`下的）修改为如下样式

{% highlight conf %}
server {
  listen 80;
  listen [::]:80;
  servername example.com;
  ...
  
  # 设置以下代码后
  # 请求 http://example.com/.well-known/xxxx 会输出 /usr/share/nginx/html/xxxx 的内容
  # 请求 http://example.com/xxxx 则会跳转到 https://example.com/xxxx
  location ^~ /.well-known/ {
    root /usr/share/nginx/html/;
    try_files $uri =404;
  }
  
  location / {
    # 将所有HTTP资源永久重定向到相应的HTTPS资源
    rewrite ^ https://$host$request_uri? permanent;
  }
  
  ...
}
{% endhighlight %}

重启 [Nginx] 服务

{% highlight sh %}
systemctl restart nginx.service
{% endhighlight %}

在`/usr/share/nginx/html/.well-known`下创建test文件

{% highlight sh %}
mkdir -p /usr/share/nginx/html/.well-known
cat >>/usr/share/nginx/html/.well-known/test<<EOF
Hello.
EOF
{% endhighlight %}

测试配置是否正确

{% highlight sh %}
curl example.com/.well-known/test
# 正确情况下输出：
# Hello.
curl example.com/.well-known/test
# 正确情况下输出：
# <html>
# <head><title>301 Moved Permanently</title></head>
# <body bgcolor="white">
# <center><h1>301 Moved Permanently</h1></center>
# <hr><center>nginx</center>
# </body>
# </html>
{% endhighlight %}

开始申请证书

{% highlight sh %}
# --email注册邮箱，初次申请需要填写，虽然不需要验证邮箱，但日后可能需要使用邮箱找回密钥，所以建议填写真实邮箱
/var/letsencrypt/letsencrypt-auto certonly -a webroot --webroot-path=/usr/share/nginx/html --email admin@example.com -d example.com -d www.example.com -d other.example.net
{% endhighlight %}

在运行 [letsencrypt-auto] 脚本后，脚本会先检查运行环境是否正确，如果缺少Python 2.7或Virtualenv的话将不会继续往下执行。一切检查正确后，脚本将会显示蓝色向导界面来让你输入一些缺失的信息，例如：是否同意相关用户协议等等。如果一开始只执行 `./letsencrypt-auto certonly` 这样的命令，向导界面将会让你输入更多的信息，例如：邮箱（第一次申请必填，用于注册服务）、域名验证方式、绑定的域名等等。在整个过程执行成功后，它将会把生成的证书放在 `/etc/letsencrypt/live/example.com` 的目录下。

申请成功的输出如下

{% highlight sh %}
IMPORTANT NOTES:
 - If you lose your account credentials, you can recover through
   e-mails sent to sammy@digitalocean.com
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your
   cert will expire on 2016-07-05. To obtain a new version of the
   certificate in the future, simply run Let's Encrypt again.
 - Your account credentials have been saved in your Let's Encrypt
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Let's
   Encrypt so making regular backups of this folder is ideal.
 - If like Let's Encrypt, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
{% endhighlight %}

#### 3. 在Nginx上部署

下面以本站域名 [shingwasix.me](https://shingwasix.me) 为例，设置网站只允许使用 [HTTPS] 进行请求，相关的Nginx配置文件代码如下：

{% highlight conf %}
server {
  listen 80;
  listen [::]:80;
  server_name shingwasix.me;
  
  location ^~ /.well-known/ {
    root /usr/share/nginx/html/;
    try_files $uri =404;
  }
  
  location / {
    # 将所有HTTP资源永久重定向到相应的HTTPS资源
    rewrite ^ https://$host$request_uri? permanent;
  }
}

server {
  listen 443 ssl spdy;
  listen [::]:443 ssl spdy;
  server_name shingwasix.me;
  ssl_certificate /etc/letsencrypt/live/shingwasix.me/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/shingwasix.me/privkey.pem;
  ...
}
{% endhighlight %}

配置修改完毕后，重新载入Nginx服务配置

{% highlight sh %}
systemctl reload nginx.service 
{% endhighlight %}

此时，我们在浏览器上打开[http://shingwasix.me](http://shingwasix.me)将会自动跳转到[https://shingwasix.me](https://shingwasix.me)去。当你在浏览器上（以[Google Chrome]为例）看到如下图所示的小绿锁以及绿色的https字样的话，就证明已经SSL证书已经部署成功了。

![letsencrypt-04][letsencrypt-04]

> 如果浏览器上没有显示小绿锁以及绿色的https字样，而是显示灰色的https并不代表SSL证书部署失败，其可能导致该种情况出现的原因为网站上引用了其他非HTTPS资源。更具体原因可利用 Google Chrome 的 Security Overview 分析了解。

#### 4. 证书续订

证书续订的命令非常简单

{% highlight sh %}
/var/letsencrypt/letsencrypt-auto renew
{% endhighlight %}

正常情况下，离过期还有一大段时间的证书将会被忽略续订（见下面输出示例），该命令会产生一个日志文件于`/var/log/le-renewal.log`（该日志目前尚未记录任何内容，所以后面将利用其它指令把命令行输出的内容写入到指定日志上）。

{% highlight sh %}
Checking for new version...
Requesting root privileges to run letsencrypt...
   /root/.local/share/letsencrypt/bin/letsencrypt renew
Processing /etc/letsencrypt/renewal/example.com.conf

The following certs are not due for renewal yet:
  /etc/letsencrypt/live/example.com/fullchain.pem (skipped)
No renewals were attempted.
{% endhighlight %}

如果我们希望测试到期时是否能续订的成功的话，可以加上`--dry-run`参数再次运行。加上该参数后，脚本程序将会忽略现有证书的有效期，而强制续订。

{% highlight sh %}
/var/letsencrypt/letsencrypt-auto renew --dry-run
{% endhighlight %}

续订成功后，重启 [Nginx] 服务

{% highlight sh %}
systemctl restart nginx.service
{% endhighlight %}

一切演练顺利后，我们需要添加定时任务去执行证书续订

{% highlight sh %}
# 执行该命令将会进入当前用户的任务列表编辑模式，请确保当前用户为root用户或使用sudo执行
crontab -e
{% endhighlight %}

进行 [crontab] 任务列表编辑模式后，插入以下内容后保存，操作方法可参见 [Vim] 操作说明。

{% highlight sh %}
# 指定每周日凌晨1:10执行续订，并将输出内容写入到/var/log/le-renew.log上
10 1 * * 0 /var/letsencrypt/letsencrypt-auto renew >> /var/log/le-renew.log
# 指定每周日凌晨1:15重启Nginx服务（为了让续订后的证书生效）
15 1 * * 0 systemctl restart nginx.servicce
{% endhighlight %}

重启 crond 服务

{% highlight sh %}
systemctl restart crond.service
{% endhighlight %}

为了测试定时任务是否有效，我们可以先将任务的触发时间设定为一个离现在较近的时间点，并加入`--dry-run`参数测试是否可以成功续订（通过查看日志可确定是否续订成功），测试成功后再将配置还原。

#### 5. 大功告成

到此为止，所有工作均已完成，你的网站将会以安全的 [HTTPS] 协议进行传输，并且SSL证书将会自动每三个月续订一次。

你可以放下手头的工作去喝杯热咖啡了☕️☕️☕️。

如果你还是对刚才设定的定时任务不放心的话（如果你是 [强迫性疑虑] 患者），则可以将任务执行的周期设置得更短，然后再多测试观察几次，但需要注意 [Let's Encrypt] 的请求次数限制（见最后的相关阅读）。

经过一番折腾之后，你又可以放下手头的工作去优雅地品尝82年的拉菲了🍷🍷🍷。

### 衍生网站

目前，我所了解到由 [Let's Encrypt] 衍生出来的网站有以下两个,两个网站均基于 [letsencrypt] 项目制作而成，用于在线上申请证书。

#### 1. SSL For Free

免费 letsencrypt 证书在线申请网站，需要注册账号登录后才可操作。可在线申请证书、查看活动证书列表（使用该网站申请的）、查看过期证书列表（使用该网站申请的）、查看已删除证书列表（使用该网站申请的），该网站帐号创建后无法删除。

网址：[https://www.sslforfree.com](https://www.sslforfree.com)

#### 2. Get HTTPS for free

同样是免费 letsencrypt 证书在线申请网站，虽然该网站样式较为简陋，但其采用了 [MIT许可证] 于 [GitHub] 上开源并声称不含任何追踪使用痕迹的代码，申请过程无需注册账号，相对于其他闭源的衍生网站来看更为快捷和安全。

网址：[https://gethttpsforfree.com](https://gethttpsforfree.com)

***⚠️[Let's Encrypt] 一直倡导开发者利用工具进行证书自动化部署，所以官方本身并不提倡使用Web服务进行在线证书申请，同时也不提供在线申请服务。***

### 相关阅读

[Let's Encrypt] 请求次数限制：[https://community.letsencrypt.org/t/rate-limits-for-lets-encrypt/6769](https://community.letsencrypt.org/t/rate-limits-for-lets-encrypt/6769)

[Let's Encrypt]: https://letsencrypt.org/
[Mozilla]: https://www.mozilla.org/
[Google Chrome]: https://www.google.com/chrome/
[Cisco]: http://www.cisco.com/
[Facebook]: https://www.facebook.com/
[GitHub]: https://github.com/
[@letsencrypt]: https://twitter.com/letsencrypt
[IE 6/XP]: https://www.ssllabs.com/ssltest/viewClient.html?name=IE&version=6&platform=XP
[Nginx]: https://zh.wikipedia.org/zh/Nginx
[CentOS 7.2]: https://zh.wikipedia.org/wiki/CentOS
[HTTPS]: https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%AE%89%E5%85%A8%E5%8D%8F%E8%AE%AE
[letsencrypt]: https://github.com/letsencrypt/letsencrypt
[letsencrypt-auto]: https://github.com/letsencrypt/letsencrypt/blob/master/letsencrypt-auto
[Web Server]: https://en.wikipedia.org/wiki/Web_server
[crontab]: http://linuxtools-rst.readthedocs.org/zh_CN/latest/tool/crontab.html
[Vim]: https://zh.wikipedia.org/wiki/Vim
[强迫性疑虑]: http://baike.baidu.com/view/1366202.htm
[MIT许可证]: https://zh.wikipedia.org/wiki/MIT%E8%A8%B1%E5%8F%AF%E8%AD%89

[letsencrypt-00]: /assets/note/letsencrypt-00.jpg
[letsencrypt-01]: /assets/note/letsencrypt-01.jpg
[letsencrypt-02]: /assets/note/letsencrypt-02.jpg
[letsencrypt-03]: /assets/note/letsencrypt-03.jpg
[letsencrypt-04]: /assets/note/letsencrypt-04.jpg