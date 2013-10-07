---
layout: post
title: "Github Pages搭建个人博客"
description: "总结了使用jekyll在github上搭建个人博客的过程，使用了jekyll bootstrap模版，以及该模版提供的主题，后期对主题有进一步的修改"
category: "jekyll"
tags: [github, jekyll, git]
---
{% include JB/setup %}

本人记忆力实在太差，做了这个博客的过程还是记下来，以备后来吧。

[msysgit]: https://code.google.com/p/msysgit/downloads/list?q=full+installer+official+git
[RubyInstaller]: http://rubyforge.org/frs/?group_id=167
[DevKit]: https://github.com/oneclick/rubyinstaller/downloads/
   
##工具和环境
**Windows 8.1**   
[**msysgit 1.8.3**] [msysgit]   
Github使用git作为版本控制工具，这是windows环境下的git工具。   
附: 远程管理git的相关有用文章：   
[https://github.com/Kunena/Kunena-Forum/wiki/Create-a-new-branch-with-git-and-manage-branches](https://github.com/Kunena/Kunena-Forum/wiki/Create-a-new-branch-with-git-and-manage-branches)   
[http://www.cnblogs.com/springbarley/archive/2012/11/03/2752984.html](http://www.cnblogs.com/springbarley/archive/2012/11/03/2752984.html)   
**jeklly 1.2.1**    
Jekyll是一种简单的、适用于博客的、静态网站生成引擎。它使用一个模板目录作为网站布局的基础框架，支持Markdown、Textile等标记语言的解析，提供了模板、变量、插件等功能，最终生成一个完整的静态Web站点。   
jekyll与github的关系：GitHub Pages一个由 GitHub提供的用于托管项目主页或博客的服务，jekyll是后台所运行的引擎。       
[**RubyInstaller 1.9.3**] [RubyInstaller]   
[**DevKit 4.5.2**] [DevKit]   
DevKit是windows平台下编译和使用本地C/C++扩展包的工具。它就是用来模拟Linux平台下的make,gcc,sh来进行编译。但是这个方法目前仅支持通过RubyInstaller安装的Ruby。   
[**Liquid**](https://github.com/shopify/liquid/wiki/liquid-for-designers)   
Jekyll使用Liquid模板语言，{{page.title}}表示文章标题，{{content}}表示文章内容。我们可以用两种Liquid标记语言：输出标记（output markup）和标签标记 (tag markup)。输出标记会输出文本（如果被引用的变量存在），而标签标记不会。输出标记是用双花括号分隔，而标签标记是用花括号-百分号对分隔。

##配置环境
官方的教程大多数都是针对Linux/Unix类系统来讲的，在windows下开发需要先安装[msysgit]，在安装好的git bash中可以得到类似Linux的环境。   

##安装软件
1. 下载安装[RubyInstaller]  到`C:\Ruby193`，path中配置`C:\Ruby193\bin`目录，并在命令行中升级gem:
		
		gem update --system
		
2. 下载安装[DevKit]到`C:\DevKit`，进入该目录运行安装：
		
		ruby dk.rb init
		ruby dk.rb install
		
3. 安装Jekyll：
		
		gem install jekyll
		
	检测是否安装成功：
		
		jekyll --version
		
4. 安装用来解析Markdown的Rdiscount包：
		
		gem install rdiscount


##在Github申请个人页面
1. 使用github pages
2. 申请域名并连接到个人主页

以上详见官网: [Github Help](https://help.github.com/categories/20/articles)   

##使用Jekyllbootstrap
使用jekyllbootstrap框架可以快速生成Jekyll网站   
详见[Jekyllbootstrap官网教程](http://jekyllbootstrap.com/)   
该网站中包括如何下载使用Jekyllbootstrap代码（git clone...),更改远程源到自己的github代码库（git remote set-url..., 注意：现在github的个人页面的后缀已经是.io而不是.com了，这一点很多旧博客和网站都是有过时的）,如何写博，改换网站主题，等等，一步步照着做即可。   

###其它配置
在使用Jekyllbootstrap框架后，有几个问题加工一下:
####去除页面上Disqus评论框架的Discovery box部分
Disqus为了增强社区氛围增加了这个，去除的方法是：
1. 在disqus网站上注册登录自己的账号，进去后add site，填上URL，site name, short site name（这一项是disqus网站要求的ID)；
2. 进入http://"short-name".disqus.com/admin/settings/discovery/ 选择 Just Comments，然后保存；
3. 在`.\_config.yml`中更改disqus的short-name为刚才在disqus上设置的short site name.
####编译时有中文错误
错误：遇到一个`liquid exception: invalid byte sequence in gbk`的错误
解决：找到Jekyll的目录`D:\Ruby193\lib\ruby\gems\1.9.1\gems\jekyll-1.2.1\lib\jekyll`
找到文件`convertible.rb`把这一句：
	
	self.content = File.read(File.join(base, name))
	
改成：
	
	self.content = File.read(File.join(base, name), :encoding => "utf-8")
	
如果有更多调试错误，见[Jekyll 本地调试之若干问题](http://chxt6896.github.io/blog/2012/02/13/blog-jekyll-native.html)

##参考资料
Github Pages: [http://pages.github.com/](http://pages.github.com/)   
Jekyll: [http://jekyllrb.com/](http://jekyllrb.com/)   
Jekyllbootstrap: [http://jekyllbootstrap.com/](http://jekyllbootstrap.com/)   
Markdown语法：[http://wowubuntu.com/markdown/index.html#p](http://wowubuntu.com/markdown/index.html#p)   
本地测试地址：[http://localhost:4000/](http://localhost:4000/)   