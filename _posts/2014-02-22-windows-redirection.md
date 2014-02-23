---
layout: post
title: "Windows Command重定向的原理和一般用法"
description: "对比Unix和Windows的命令行的重定向方法和使用方法"
category: "windows"
tags: [Unix, Windows, redirection, cmd]
---
{% include JB/setup %}

在测试程序时，为了避免从命令行重复输入数据和把输据能够打印到文件，在Unix和Windows下均可以通过重定向来实现。
重定向句柄：   

<table class="table table-bordered table-striped table-condensed">
	<tr><td><b>句柄</b></td><td><b>句柄号</b></td><td><b>说明</b></td></tr>
	<tr><td>STDIN</td><td>0</td><td>标准输入，默认从键盘输入</td></tr>
	<tr><td>STDOUT</td><td>1</td><td>标准输出，默认输出到命令提示符窗口</td></tr>
	<tr><td>STDERR</td><td>2</td><td>标准错误输出，默认输出到命令提示符窗口</td></tr>
	<tr><td>UNDEFINED</td><td>3-9</td><td>这些句柄由应用程序和各个具体工具单独定义</td></tr>
</table>

详细的英文介绍见微软网页：   
[http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/redirection.mspx?mfr=true](http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/redirection.mspx?mfr=true)
   

###栗子

	
	//stdout to console
	echo test
	
	//stdout to file
	echo test 1>D:\test.txt
	
	//stdin from file, use cin in MyApp.exe
	MyApp.exe 0<D:\test.txt
	

#####说明
每次重定向只对当前命令有效，命令执行完后会重新恢复standard输入输出。

###备份机制
在每次对输入输出进行重定向时，在命令执行前依次处理，每次处理都会三步：
1.把被重定向的句柄复制到从0开始向后第一个不为空的句柄上
2.赋值
3.备份还原

#####说明
备份过程是一个栈过程，可能会有意想不到的问题，详见blog:   
[http://hi.baidu.com/youziwenhan/item/88f115183806b8727a5f25cb](http://hi.baidu.com/youziwenhan/item/88f115183806b8727a5f25cb)
