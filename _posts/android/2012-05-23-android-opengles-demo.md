---
layout: post
title: Android OpenGLES 示例
date: 2012-05-23 02:21:24.000000000 +08:00
categories:
- android
tags:
- android
- gles
- opengles
status: publish
type: post
published: true
author: Shingwa Six
---

在android中使用opengles进行开发,主要需要三步.

1.新建一个继承自GLSurfaceView的视图,重写构造函数和onTouchEvent方法.

{% highlight java %}
public class VortexView extends GLSurfaceView {
	private VortexRenderer _renderer;
	
	public VortexView(Context context) {
		super(context);
		_renderer = new VortexRenderer();
		setRenderer(_renderer);
	}
	
	//该方法用于响应触摸事件
	public boolean onTouchEvent(final MotionEvent event) {
		if (event.getAction() == MotionEvent.ACTION_DOWN) {
			
		}
		if (event.getAction() == MotionEvent.ACTION_MOVE) {
			
		}
		return true;
	}
}
{% endhighlight %}

2.新建一个继承自GLSurfaceView.Rederer的渲染器,重写onSurfaceCreated,onSurfaceChanged,onDrawFrame

{% highlight java %}
public class VortexRenderer implements GLSurfaceView.Renderer {
	//渲染器创建时执行,用于初始化.
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
	
	}
	
	//屏幕大小改变时执行
	public void onSurfaceChanged(GL10 gl, int width, int height) {
	
	}
	
	//渲染帧时执行
	public void onDrawFrame(GL10 gl) {
	
	}
}
{% endhighlight %}

3.将GLSurfaceView的实例添加到活动对象上.

{% highlight java %}
public class test extends Activity {
	private VortexView _vortexView;
	
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		_vortexView = new VortexView(this);
		setContentView(_vortexView);
	}
}
{% endhighlight %}

4.最终运行效果图

[![android_opengles_demo](/assets/android/android_opengles_demo.jpg)](/assets/android/android_opengles_demo.jpg)

5.demo工程下载 

[android_opengles_demo.zip]({{ site.url }}/assets/android/android_opengles_demo.zip)
