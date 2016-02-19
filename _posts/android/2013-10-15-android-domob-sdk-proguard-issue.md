---
layout: post
title: Android项目中混淆编译含多盟SDK时出错的解决方法
date: 2013-10-15 11:07:00.000000000 +08:00
categories:
- android
tags:
- 多盟
- 混淆
status: publish
type: post
published: true
author: Shingwa Six
---

再android应用中嵌入了多盟SDK,并使用混淆编译时有可能会出现如下错误:

{% highlight java %}
[2013-10-15 10:45:48 - XXXX] java.io.IOException: Please correct the above warnings first.
[2013-10-15 10:45:48 - XXXX] 	at proguard.Initializer.execute(Initializer.java:321)
[2013-10-15 10:45:48 - XXXX] 	at proguard.ProGuard.initialize(ProGuard.java:211)
[2013-10-15 10:45:48 - XXXX] 	at proguard.ProGuard.execute(ProGuard.java:86)
[2013-10-15 10:45:48 - XXXX] 	at proguard.ProGuard.main(ProGuard.java:492)
[2013-10-15 10:46:14 - XXXX] Proguard returned with error code 1. See console
[2013-10-15 10:46:14 - XXXX] Warning: cn.domob.android.ads.a: can't find referenced method 'void setPluginsEnabled(boolean)' in class android.webkit.WebSettings
[2013-10-15 10:46:14 - XXXX] Warning: cn.domob.android.ads.a.d: can't find referenced method 'void setPluginsEnabled(boolean)' in class android.webkit.WebSettings
[2013-10-15 10:46:14 - XXXX] Warning: there were 2 unresolved references to program class members.
[2013-10-15 10:46:14 - XXXX]          Your input classes appear to be inconsistent.
[2013-10-15 10:46:14 - XXXX]          You may need to recompile them and try again.
[2013-10-15 10:46:14 - XXXX]          Alternatively, you may have to specify the option 
[2013-10-15 10:46:14 - XXXX]          '-dontskipnonpubliclibraryclassmembers'.
[2013-10-15 10:46:14 - XXXX] java.io.IOException: Please correct the above warnings first.
[2013-10-15 10:46:14 - XXXX] 	at proguard.Initializer.execute(Initializer.java:321)
[2013-10-15 10:46:14 - XXXX] 	at proguard.ProGuard.initialize(ProGuard.java:211)
[2013-10-15 10:46:14 - XXXX] 	at proguard.ProGuard.execute(ProGuard.java:86)
[2013-10-15 10:46:14 - XXXX] 	at proguard.ProGuard.main(ProGuard.java:492)
{% endhighlight %}

解决方法:
=======

再Proguard配置文件里加入如下代码

`-dontwarn android.webkit.**`

因为 `void setPluginsEnabled(boolean)` 这个方法在API Level 18中已经被抛弃使用了,使用上述命令可以忽略android.webkit包内的警告.
