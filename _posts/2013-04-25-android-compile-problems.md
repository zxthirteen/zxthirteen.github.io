---
layout: post
title: "Android2.3.x的编译问题"
description: ""
category: "android"
tags: [android, compile]
---
{% include JB/setup %}

**Ubuntu11.10下编译Android-2.3.3_r1的问题：也可以用于编译2.3.4**  
刷机方法见[source.android.com](source.android.com)   
在代码完整下载后为了在机器Nexus S上运行，需要相关的wifi, GSM, 图形等驱动   
在网页[http://code.google.com/intl/zh-CN/android/nexus/drivers.html#crespogri40](http://code.google.com/intl/zh-CN/android/nexus/drivers.html#crespogri40)中下载
并进行源代码目录中运行下载的脚本   
   
编译时用lunch命令然后选择对应的平台，如果使用默认，则编译出来的供虚拟机使用。   

###可能需要的的环境变量
	
	# zhouxin for Android building
	export USE_CCACHE=1
	export ANDROID_PRODUCT_OUT=/home/zhouxin/Android/source/out/target/product/generic
	export PATH=$PATH:/home/zhouxin/Android/source/out/host/linux-x86/bin
	export SDKTOOLS=/home/zhouxin/Android/source/out/host/linux-x86/sdk/android-sdk_eng.zhouxin_linux-x86/tools
	export PATH=$PATH:$SDKTOOLS
	export ANDROID_SWT=/home/zhouxin/Android/source/out/host/linux-x86/framework
	
**以下问题均是编译过程中真实出现的问题，解决方案来自网上资料**   

**错误**   
	
	************************************************************
	You are attempting to build on a 32-bit system.
	Only 64-bit build environments are supported beyond froyo/2.2.
	************************************************************
	
这是因为在Makefile中检测了CPU的字长。直接把`build/core/main.mk`中下面的话注释掉
	
	#ifneq (64,$(findstring 64,$(build_arch)))
	#$(warning ************************************************************)
	#$(warning You are attempting to build on a 32-bit system.)
	#$(warning Only 64-bit build environments are supported beyond froyo/2.2.)
	#$(warning ************************************************************)
	#$(error stop)
	#endif
	
**错误** 
		
	Docs droiddoc: out/target/common/docs/api-stubs
	Could not load ‘clearsilver-jni’
	java.library.path = out/host/linux-x86/lib
	make: *** [out/target/common/docs/api-stubs-timestamp] Error 45
	make: *** Waiting for unfinished jobs….
	Could not load ‘clearsilver-jni’
	java.library.path = out/host/linux-x86/lib
	make: *** [out/target/common/docs/doc-comment-check-timestamp] Error 45

这是由于clearsilver在编译时如果检测到使用Java JDK 6，就使用64位编译。要避开此错误，需要修改下面四个文件：
	
	external/clearsilver/cgi/Android.mk
	external/clearsilver/java-jni/Android.mk
	external/clearsilver/util/Android.mk
	external/clearsilver/cs/Android.mk
	
把这四个Makefile中的下列语句注掉即可
	
	# This forces a 64-bit build for Java6
	# Comment by Easwy
	# LOCAL_CFLAGS += -m64
	# LOCAL_LDFLAGS += -m64
	
当然也可以把64改为32！

**错误**   
make: *** \[out/host/linux-x86/obj/SHARED_LIBRARIES/libneo_util_intermediates/neo_err.o\] Error 1   
安装包：libneo**   

**错误**   
make: *** \[out/host/linux-x86/obj/SHARED_LIBRARIES/libclearsilver-jni_intermediates/j_neo_util.o\] Error 1   
安装包：lib64z1-dev libc6-dev-amd64 g++-multilib lib64stdc++6    
	
**错误**   

host C++: libutils <= frameworks/base/libs/utils/RefBase.cpp   
frameworks/base/libs/utils/RefBase.cpp:483:67: error: passing ‘const android::RefBase::weakref_impl’ as ‘this’ argument of ‘void android::RefBase::weakref_impl::trackMe(bool, bool)’ discards qualifiers \[-fpermissive\]   
make: *** \[out/host/linux-x86/obj/STATIC_LIBRARIES/libutils_intermediates/RefBase.o\] 错误 1   
解决方法：打开Android.mk   
	
	$ gedit frameworks/base/libs/utils/Android.mk
	
将行：
	
	LOCAL_CFLAGS += -DLIBUTILS_NATIVE=1 $(TOOL_CFLAGS)
	
改为：

	LOCAL_CFLAGS += -DLIBUTILS_NATIVE=1 $(TOOL_CFLAGS) -fpermissive

	
**错误**   
	
	<command-line>:0:0: error: "_FORTIFY_SOURCE" redefined [-Werror]
	 <built-in>:0:0: note: this is the location of the previous definition
	 cc1plus: all warnings being treated as errors
	make: *** [out/host/linux-x86/obj/EXECUTABLES/obbtool_intermediates/Main.o] Error 1
	
此处编译错误是由于ubuntu 11.10采用了GCC4.6.1导致的。
**解决方法**   
修改源码目录下`/build/core/combo/HOST_linux-x86.mk`文件，将以下语句

	HOST_GLOBAL_CFLAGS += -D_FORTIFY_SOURCE=0
	
改为：
	
	HOST_GLOBAL_CFLAGS += -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0
	
	
