---
layout: post
title: php array/object转xml
date: 2013-01-27 07:14:47 +08:00
categories:
- php
tags:
- php
- xml_decode
- xml_encode
---

在网站与网站或者移动客户端交互时，我们通常会使用json格式的数组来进行交互。

php 5.0之后的版本可以使用 json_encode和json_decode 来轻松地将array或object进行json编码以及反编码。

但某些平台(系统)可能由于历史的原因或是技术的原因，只能支持xml的解析，而正巧php并没有内置将array或object进行xml编码或反编码的函数。

在google上搜索很多关于php array转xml的文章，均未能完美解决我的问题。

网上的array转xml代码基本都存在以下几个问题

+ 1.无法解决内容中存在`<`或`>`符号而导致的转换错误

+ 2.当数据存在键值为数字的数据时，解析出错

以下是我整理后，但尚未解决以上问题的代码

{% highlight php %}
<?php
function xml_encode($value, $tag = "root") {
	if( !is_array($value)
		&& !is_string($value)
		&& !is_bool($value)
		&& !is_numeric($value)
		&& !is_object($value) ) {
			return false;
	}
	function x2str($xml, $key) {
		if (!is_array($xml) && !is_object($xml)) {
			return "<$key>$xml</$key>";
		}
		$xml_str="";
		foreach ($xml as $k=>$v) {
			$xml_str .= x2str($v, $k);
		}
		return "<$key>$xml_str</$key>";
	}
	return simplexml_load_string(x2str($value, $tag))->asXml();
}

function xml_decode($xml) {
	if(!is_string($xml)) {
		return false;
	}
	$xml = @simplexml_load_string($xml);
	return $xml;
}
{% endhighlight %}

首先说一下xml中不能存在内容，换行符和制表符虽然会让xml格式看上去有点不工整，但这并不影响浏览器或其他xml解析程序去解析xml。

大小于号（`<`和`>`）是xml中用于识别节点的关键字，如果在内容中出现这两个符号的任意一个都会导致xml解析器的解析失败。

当然某些容错性好的解析器可能会识别正常，但xml作为一门严谨的语言，我们是不应该让她出现错误内容的。

所以在输出xml内容的时候，我们要将`<`和`>`分别转换为url编码格式的`%3C`和`%3E`。

代码如下

{% highlight php %}
<?php
$xml = str_replace("<", "%3C", $xml);
$xml = str_replace(">", "%3E", $xml);
{% endhighlight %}

在xml解析前的时候，只需要将`%3C`和`%3E`还原一下即可。

这时候问题又来了，如果原本内容中存在`%3C`或者`%3E`的话，会被客户端误会了的，所以我们还需要将`%`号先转换为url编码`%25`。

代码如下，先后顺序不能调转

{% highlight php %}
<?php
$xml = str_replace("%", "%25", $xml);
$xml = str_replace("<;", "%3C", $xml);
$xml = str_replace(">;", "%3E", $xml);
{% endhighlight %}

再来看问题2，键值为数字时会解析错误，这是因为xml的键值不允许以数字为看头，我们只需在输出的时候，修改一下数字键值名则可。

思来想去，最后得出在数字前加一个下划线`_`作为键值最合适不过了。

代码如下

{% highlight php %}
<?php
if(is_numeric($k)) {
	$k = "_".$k;
}
{% endhighlight %}

下面来进行以下测试，测试代码如下

{% highlight php %}
<?php
@header("Content-type:application/xml");

$test['string']='文本';
$test['ss']='12:30';
$test[123]=123123123;
$test['asdasd']=true;
$test[3]=array(
	'entree'=>"salad\n;s<>s;#$$123123sss",
	'maincourse'=>'steak'
);
echo xml_encode((object)$test,"hash");
{% endhighlight %}

测试结果，以下时chrome浏览器查看到的内容，xml解析成功并显示正常。

![php-array-to-xml-1](/assets/php/php-array-to-xml-1.jpg)

我们再看一下页面源码

![php-array-to-xml-2](/assets/php/php-array-to-xml-2.jpg)

在这里，我意外地发现了中文内容输出的并非中文，而是一些html转义符。这时候我想起了html转义符是会将特殊符号（包括`<`和`>`）转换为不与html标签冲突的字符。于是我改用了php内置的html文本转义函数

{% highlight php %}
<?php
htmlspecialchars($xml);
{% endhighlight %}

使用了一下，效果还不错，以下是chrome浏览器解析后显示的内容

![php-array-to-xml-3](/assets/php/php-array-to-xml-3.jpg)

不但解析正常，还在在浏览器上直接看到原始数据的`<`和`>`，以下附上最终修改完整的代码：

{% highlight php %}
<?php
/*
Function : xml_encode,xml_decode
Author : Shingwa Six
Source code from http://blog.waaile.com/array-to-xml
*/

function xml_encode($value, $tag = "root") {
	if( !is_array($value)
		&& !is_string($value)
		&& !is_bool($value)
		&& !is_numeric($value)
		&& !is_object($value) ) {
			return false;
	}
	function x2str($xml, $key) {
		if (!is_array($xml) && !is_object($xml)) {
			return "<$key>".htmlspecialchars($xml)."</$key>";
		}
		$xml_str = "";
		foreach ($xml as $k=>$v) {
			if(is_numeric($k)) {
				$k = "_".$k;
			}
			$xml_str .= x2str($v,$k);
		}
		return "<$key>$xml_str</$key>";
	}
	return simplexml_load_string(x2str($value, $tag))->asXml();
}

function xml_decode($xml) {
	if(!is_string($xml)) {
		return false;
	}
	$xml = @simplexml_load_string($xml);
	return $xml;
}
{% endhighlight %}

xml_decode反编码出来的是SimpleXmlElemnet对象，怎么用就看你喜欢了。

github地址 [https://github.com/shingwasix/PHP-array-to-xml](https://github.com/shingwasix/PHP-array-to-xml)

转载或使用该代码请保留原作者信息，谢谢！
