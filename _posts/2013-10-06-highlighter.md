---
layout: post
title: "google-code-prettify高亮Jekyll代码"
description: "使用google-code-prettify高亮Jekyll代码，使用了比较暗的desert主题，这也是我在vim上最喜欢的主题色"
category: "jekyll"
tags: [jekyll, highlight]
---
{% include JB/setup %}

[google-code-prettify](https://code.google.com/p/google-code-prettify/)
的高亮功能用起来很方便，主要就是使用了jQuery和自己的一些js文件和一个css文件。
##主要步骤
1. 下载[google-code-prettify](https://code.google.com/p/google-code-prettify/)
	并解压缩一堆文件到`/assest/google-code-prettify/`下
2. 打开当前使用的jekyll bootstrap theme的`default.html`文件，比如现在使用主题twitter，就打开`\_includes\themes\twitter\default.html`
	文件在`<head>`内加入：
	
		<!-- add as hignlightier-->
		<link href="/assets/google-code-prettify/prettify.css" rel="stylesheet" type="text/css" media="all">
		<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
		<script type="text/javascript" src="/assets/google-code-prettify/prettify.js"></script>
		<script type="text/javascript">
			$(function(){
				$("pre").addClass("prettyprint linenums");
				prettyPrint();
			});
		</script>
	
	这种方法只修改了当前jekyllbootstrap主题的default.html文件，换主题后应该需要相应修改。现在，就可以使用`<pre>`和`</pre>`标签进行高亮了
	
		<pre class="prettyprint linenums">
		// code here
		</pre>
		
3. 但这样会有些问题，就是在code区域内书写html代码的时候，html标签会被浏览器认为是标签而不是代码的字符。
	而markdown的语法写的代码其实已经解决了这个问题，上文中的`$("pre").addClass("prettyprint linenums");`
	已经解决了这个问题，可以直接用markdown标准的缩进来写代码：
	
		#include "stdio.h"
		int main() {
			int a;
			int b = 10;
			printf("this is time");
		}
		
4. 在google-code-prettify默认的设置中，每5行显示一个行号，如果想全部显示，在`prettify.css`的`<li>`标签中
	添加
	
		li { list-style-type: decimal }
		
5. google-code-prettify已经安装完成了，但是是浅色背景，如果要给google-code-prettify
	换背景，进入[themes gallery](http://google-code-prettify.googlecode.com/svn/trunk/styles/index.html)
	选择（比如desert）把css中的内容粘贴到`\assets\google-code-prettify\prettify.css`替换
	原来的全部内容即可。
6. 结束！	