---
layout: post
title: iOS开发判断UIWebView载入完成
date: 2016-01-29 11:32:00 +08:00
categories:
- iOS
author: Shingwa Six
---

在iOS中```UIWebView```每成功载入一个URL资源就会回调一次```webViewDidFinishLoad```，载入的资源种类包括但不限于html、jpeg、png、css、js，而一个完整页面通常会包含多个URL资源，所以我们无法简单地使用```webViewDidFinishLoad```来判断一个页面完全载入完毕。

下面提供两种解决方法

1.利用Javascript代码查询页面状态

{% highlight objc %}
- (void)webViewDidFinishLoad:(UIWebView *)webView
{
    if ([[webView stringByEvaluatingJavaScriptFromString:@"document.readyState"] isEqualToString:@"complete"]) {
    //do something
    }
}
{% endhighlight %}


2.利用UIWebView的```loading```属性判断

{% highlight objc %}
@property (nonatomic, readonly, getter=isLoading) BOOL loading;
{% endhighlight %}

{% highlight objc %}
- (void)webViewDidFinishLoad:(UIWebView *)webView
{
    if (!webView.isLoading) {
    //do something
    }
}
{% endhighlight %}