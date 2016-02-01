---
layout: post
title: vsftpd虚拟用户配置
date: 2013-09-16 13:26:13.000000000 +08:00
categories:
- note
- unix
tags: []
status: publish
type: post
published: true
author: Shingwa Six
---

进入vsftpd配置目录,linux一般在```/etc/vsftpd```下

1.新建```users.txt```文件(如果不存在的话),添加如下文本.其中基数行为用户名,偶数行为密码

users.txt

{% highlight sh %}
test01
123456
test02
654321
{% endhighlight %}

2.在/var下分别为每个用户创建一个用户文件夹,分别为```test01```和```test02```

3.根据```users.txt```新建用户配置文件.新建users_config文件夹(如果不存在的话),并新建两个文件,分别为```test01.txt```和```test02.txt```,内容如下:

users_config/test01.txt

{% highlight sh %}
local_root=/var/test01
anon_world_readable_only=YES
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
{% endhighlight %}

users_config/test02.txt

{% highlight sh %}
local_root=/var/test02
anon_world_readable_only=YES
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
{% endhighlight %}

4.新建```chroot_list```文件(如果不存在),在上边加上之前设置的用户名,该文件里的用户将无法查看其```local_root```以外的目录

chroot_list

{% highlight sh %}
test01
test02
{% endhighlight %}

5.执行命令将```users.txt```写入到vsftpd数据库中

{% highlight sh %}
db_load -T -t hash -f users.txt users.db
{% endhighlight %}

6.最后,测试ftp账号

如无意外,连接后看到的远程站点是```/```,这是步骤4所起到的作用,否则将会显示为诸如```/var/test01```这样.

[![vsftpd.jpg][1]][1]

[1]: /assets/*nix/vsftpd.jpg
