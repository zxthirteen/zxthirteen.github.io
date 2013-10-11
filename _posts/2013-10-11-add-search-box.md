---
layout: post
title: "Jekyll网站增加搜索框"
description: "为Jekyll网站增加搜索框，用jQuery实现打开google搜索页面"
category: "jekyll"
tags: [jekyll, jQuery, search]
---
{% include JB/setup %}

本文参考博客[我的jekyll配置和修改](http://blog.javachen.com/post/2013/08/31/my-jekyll-config/)
和[Create Simple Search box](http://truongtx.me/2012/12/28/jekyll-create-simple-search-box/)，使用了jQuery实现，关于jQurey的用法，最好的javaScritp和jQuery教程见[w3schools](www.w3schools.com)   


####增加search box的html代码
在当前所用主题的`default.html`里添加，如当前使用主题的该文件`._includes\themes\twitter\default.html`的navigate bar中：
	
	<form class="navbar-search pull-left" id="search-form">
		<input type="text" id="google-search" class="search-query" placeholder="Search" />
	</form>
	
####增加相应javascript
	
	$("#search-form").submit(function(){
		var query = document.getElementById("google-search").value;
		window.open("http://google.com/search?q=" + query+ "%20site:" + "http://zxthirteen.github.io");
	});
	
####一个问题
在按上述方法实现后，在运行时发现输入内容后点击提交不起作用，经F12调试发现是jQuery中`var query =`这条语句出错，说对象不存在或为null。   
**解决**  
这是由于在运行到这条语句时，对象`google-search`可能还不存在，所以应该在页面加载完成后再运行对应jQuery语句，修改如下：
	
	$(document).ready(function(){
		$("#search-form").submit(function(){
			var query = document.getElementById("google-search").value;
			window.open("http://google.com/search?q=" + query+ "%20site:" + "http://zxthirteen.github.io");
		});
	});
	
`$(document).ready(function(){}`表示在页面ready后运行的函数。   
   
####OK！