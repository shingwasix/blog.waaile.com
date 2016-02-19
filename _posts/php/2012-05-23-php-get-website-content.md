---
layout: post
title: PHP获取其他站点页面数据
date: 2012-05-23 02:58:13.000000000 +08:00
categories:
- php
tags:
- php
- 跨域访问
status: publish
type: post
published: true
author: Shingwa Six
---

在某些情况下,我们可能需要在A网站的PHP页面内获取B网站的PHP页面内容.当然我们可以在前台利用Ajax技术获取B网站页面内容,但如果我们不希望用户知道A网站到底获取了哪个网站并且传了什么值的时候,我们就有必要在请求A网站的PHP页面的时候把B页面的内容截获下来.

这里主要是用了一些Socket远程读写的方法,代码如下:

{% highlight php %}
<?php
//请求的域名
$domain = "www.baidu.com";

if(gethostbyname($domain) == $domain) {
	die("无法访问服务器.");
}

//请求的端口
$port = 80;

//请求的页面
$uri = "/index.php";

//要post的数据 
$data = array(
	'page' => $_POST['page'], 
	'message' => $_POST['push_message'],
); 

$flag = 0;

//构造要post的字符串 
foreach ($data as $key => $value) { 
	if ($flag != 0) {
		$params .= "&";
	}
	
	$params .= $key . "=";
	$params .= urlencode($value); 
	$flag = 1;
}

//获取post的数据长度
$length = strlen($params);  

//打开sock连接
$sock = fsockopen($domain, $port, $errno, $errstr, 30) or die("$errstr ($errno)\n");  

//构造post请求的头 
$header = "POST {$uri} HTTP/1.1\r\n"; //制定为 POST的方法提交数据 及要提交到的页面和协议类型
$header .= "Host:{$domain}\r\n";   //定义主机
$header .= "Referer:http://www.baidu.com/test.php\r\n"; //Referer信息,如果提交到的地址和当前不在一个域内,并且远程主机起用了防盗链,此值就很重要,必须为远程主机的域名 phperz~com 
$header .= "Content-Type: application/x-www-form-urlencoded\r\n"; //说明这个请求为POST
$header .= "User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.2; SV1; .NET CLR 1.1.4322)\r\n"; //用户代理,某些网站会根据代理返回相应页面,如android手机请求,则代理中带有android字眼,网站则返回android适用的页面
$header .= "Content-Length: ".$length."\r\n"; //提交的数据长度
$header .= "Connection: Close\r\n\r\n";//关闭连接:注意.此处必须得有四个回车, 或着在下面post的数据前加上二个加车 \r\n 
$header .= $params."\r\n";//添加post的字符串 

//post数据
fputs($sock, $header);  

$inheader = 1; 
$lines='';

while (!feof($sock)) { 
	$line = fgets($sock,1024); //去除请求包的头只显示页面的返回数据 
	
	if ($inheader &amp;&amp; ($line == "\n" || $line == "\r\n")) {
		$inheader = 0;  
	} 
	
	if ($inheader == 0) { 
	$lines .= $line;
	} 
}

echo $lines;
{% endhighlight %}

其中lines就是从B网站页面中获取的内容.
