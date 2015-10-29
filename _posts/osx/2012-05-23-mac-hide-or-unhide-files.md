---
layout: post
title: Mac下显示和隐藏文件
date: 2012-05-23 02:49:43 +08:00
categories:
- note
tags:
- mac
- 使用技巧
---

打开Terminal(终端),输入以下命令

开启显示隐藏文件:
{% highlight sh %}
defaults write com.apple.finder AppleShowAllFiles -bool true
{% endhighlight %}

禁止显示隐藏文件:
{% highlight sh %}
defaults write com.apple.finder AppleShowAllFiles -bool false
{% endhighlight %}

或者使用以下命令

开启显示隐藏文件:
{% highlight sh %}
defaults write com.apple.finder AppleShowAllFiles YES
{% endhighlight %}

禁止显示隐藏文件:
{% highlight sh %}
defaults write com.apple.finder AppleShowAllFiles NO
{% endhighlight %}

执行完上述命令后,还需要重启Finder

可使用命令行 ```killall Finder``` (注意:Finder必须是首字母大写)
或者 点击左上角的苹果标志-->强制退出-->Finder-->重新启动
