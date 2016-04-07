---
layout: post
title: 在CentOS 7上安装Jekyll
date: 2016-04-06 14:09 +08:00
categories:
- note
status: publish
type: post
published: true
author: Shingwa Six
---

首先使用 [Yum] 安装 [RubyGems]

{% highlight sh %}
yum install -y rubygems
{% endhighlight %}

查看 [RubyGems] 版本

{% highlight sh %}
gem --version
{% endhighlight %}

输出版本号则安装正常

{% highlight sh %}
2.6.3
{% endhighlight %}

如果主机存托管在国内，那还需要使用淘宝的 [RubyGems] 镜像，地址：[https://ruby.taobao.org/](https://ruby.taobao.org/)

通过 [RubyGems] 安装 [Jekyll]

{% highlight sh %}
gem install jekyll
{% endhighlight %}

此时安装可能会提示以下错误，原因是缺少 [Ruby] 开发套件

{% highlight sh %}
ERROR:  Error installing jekyll:
ERROR: Failed to build gem native extension.

    /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby extconf.rb
mkmf.rb can't find header files for ruby at /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/lib/ruby/ruby.h


Gem files will remain installed in /Library/Ruby/Gems/1.8/gems/fast-stemmer-1.0.1 for inspection.
Results logged to /Library/Ruby/Gems/1.8/gems/fast-stemmer-1.0.1/ext/gem_make.out
{% endhighlight %}

解决办法：使用 [Yum] 安装 [Ruby] 开发套件后，再次安装 [Jekyll]

{% highlight sh %}
yum install -y ruby-devel
gem install jekyll
{% endhighlight %}

再次安装可能会提示载入 [json_pure] 模块文件失败，错误信息如下

{% highlight sh %}
Fetching: jekyll-3.1.2.gem (100%)
Successfully installed jekyll-3.1.2
/usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require': cannot load such file -- json/pure (LoadError)
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/share/gems/gems/json-1.7.7/lib/json.rb:60:in `rescue in <module:JSON>'
	from /usr/share/gems/gems/json-1.7.7/lib/json.rb:57:in `<module:JSON>'
	from /usr/share/gems/gems/json-1.7.7/lib/json.rb:54:in `<top (required)>'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/text.rb:16:in `<top (required)>'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/code_object.rb:28:in `<class:CodeObject>'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/code_object.rb:26:in `<top (required)>'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/generator/markup.rb:59:in `<top (required)>'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/generator/darkfish.rb:6:in `<top (required)>'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/rdoc.rb:563:in `<top (required)>'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/local/share/ruby/site_ruby/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/rubygems_hook.rb:64:in `load_rdoc'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/rubygems_hook.rb:229:in `setup'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/rubygems_hook.rb:142:in `generate'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/rubygems_hook.rb:54:in `block in generation_hook'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/rubygems_hook.rb:53:in `each'
	from /usr/share/gems/gems/rdoc-4.0.0/lib/rdoc/rubygems_hook.rb:53:in `generation_hook'
	from /usr/local/share/ruby/site_ruby/rubygems/request_set.rb:189:in `call'
	from /usr/local/share/ruby/site_ruby/rubygems/request_set.rb:189:in `block in install'
	from /usr/local/share/ruby/site_ruby/rubygems/request_set.rb:188:in `each'
	from /usr/local/share/ruby/site_ruby/rubygems/request_set.rb:188:in `install'
	from /usr/local/share/ruby/site_ruby/rubygems/commands/install_command.rb:205:in `install_gem'
	from /usr/local/share/ruby/site_ruby/rubygems/commands/install_command.rb:255:in `block in install_gems'
	from /usr/local/share/ruby/site_ruby/rubygems/commands/install_command.rb:251:in `each'
	from /usr/local/share/ruby/site_ruby/rubygems/commands/install_command.rb:251:in `install_gems'
	from /usr/local/share/ruby/site_ruby/rubygems/commands/install_command.rb:158:in `execute'
	from /usr/local/share/ruby/site_ruby/rubygems/command.rb:310:in `invoke_with_build_args'
	from /usr/local/share/ruby/site_ruby/rubygems/command_manager.rb:169:in `process_args'
	from /usr/local/share/ruby/site_ruby/rubygems/command_manager.rb:139:in `run'
	from /usr/local/share/ruby/site_ruby/rubygems/gem_runner.rb:55:in `run'
	from /usr/bin/gem:21:in `<main>'
{% endhighlight %}

解决办法：安装 [json_pure] 后，再次安装 [Jekyll]
{% highlight sh %}
gem install json_pure
gem install jekyll
{% endhighlight %}

查看 [Jekyll] 版本号

{% highlight sh %}
jekyll --version
{% endhighlight %}

安装正常则输出如下

{% highlight sh %}
jekyll 3.1.2
{% endhighlight %}

Done.🍺

[Yum]: https://zh.wikipedia.org/zh/Yellowdog_Updater,_Modified
[RubyGems]: https://rubygems.org/
[Ruby]: https://www.ruby-lang.org/zh_cn/
[Jekyll]: http://jekyllcn.com/
[json_pure]: https://rubygems.org/gems/json_pure