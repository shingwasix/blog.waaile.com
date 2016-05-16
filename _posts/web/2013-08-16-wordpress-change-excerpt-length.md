---
layout: post
title: WordPress首页摘要长度修改
date: 2013-08-16 02:32:41.000000000 +08:00
categories:
- note
- php
tags:
- wordpress
- 摘要长度
status: publish
type: post
published: true
author: Shingwa Six
---

WordPress默认的首页文章摘要文字长度为55，由于部分主题的首页文章摘要位置较大，仅显示55个字符会感觉空空荡荡一样。

所以我希望将首页的摘要文字长度变大，经常一番Google后，获知具体做法如下：

用任意文件编辑工具打开WordPress下的`wp-includes/formatting.php`这个文件，定位到以下代码处

{% highlight php %}
$excerpt_length = apply_filters('excerpt_length', 55);
$excerpt_more = apply_filters('excerpt_more', ' ' . '[...]');
{% endhighlight %}

将55修改为希望的长度大小即可。

**⚠️注意：每次更新wordpress后都需要重新修改。**
