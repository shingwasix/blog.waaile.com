---
layout: post
title: iOS开发控制屏幕亮度
date: 2016-01-29 11:37:00 +08:00
categories:
- iOS
---

通过修改UIScreen下的brightness属性（iOS5及以上版本可用）。该属性注明称只支持主屏幕修改，就是只能修改iOS设备的屏幕亮度，不能修改外接显示器的亮度。取值范围是0.0～1.0，0.0为最暗亮度，1.0为最亮亮度。

{% highlight objc %}
@property(nonatomic) CGFloat brightness NS_AVAILABLE_IOS(5_0);        // 0 .. 1.0, where 1.0 is maximum brightness. Only supported by main screen.     
{% endhighlight %}

用法

{% highlight objc %}
[[UIScreen mainScreen] setBrightness:0.5];  
{% endhighlight %}

⚠️注意：该亮度修改系统全局有效，意味着你修改了之后即便关闭了应用，亮度还是保持你之前使用应用调整的值。对于某些只需要临时调整亮度的界面，如视频播放界面、二维码展示界面，我们一般需要在界面消失后恢复原始亮度。