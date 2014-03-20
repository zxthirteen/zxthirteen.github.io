---
layout: post
title: "在Mac中安装jekyll"
description: ""
category: "jekyll"
tags: [jekyll, mac, ruby, rvm, gem]
---
{% include JB/setup %}

在之前的windows中安装过jekyll，现在在mac上安装发现还是有些容易出错的地方，尤其是感谢伟大的“墙”，让我的本来很简单的安装过程变得非常艰难。   
   
#####参考链接：
[http://davidensinger.com/2013/03/installing-jekyll/](http://davidensinger.com/2013/03/installing-jekyll/)   
[http://ruby-china.org/wiki/install_ruby_guide](http://ruby-china.org/wiki/install_ruby_guide)   
   
安装jekyll前是首先要安装ruby的，其实在mac中本来就有ruby，但是需要安装自己的ruby来使用jekyll，这里是个疑点，因为用`ruby --version`是可以看到mac原生就是有ruby的，而且是很新的版本。   

###安装步骤 
在App Store安装xcode，安装Command Line Tools   
   
安装[homebrew](http://mxcl.github.com/homebrew)   
   
安装[rvm](rvm.io)， 也就是ruby version manager，但是因为墙，所以要用[这里](http://blog.csdn.net/watkinsong/article/details/7770391)的方法。  
   
安装必要的包
    
    rvm requirements
    
用rvm安装ruby
    
    rvm install ruby
    
最后安装jekyll！
    
    gem install jekyll
    
后面的常用命令就和windows下一样的，常用的命令是：
    
    jekyll build
    jekyll serve
    rake post title="something"
    

