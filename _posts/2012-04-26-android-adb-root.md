---
layout: post
title: "Android Linux kernel编译刷机以及adb root权限的原理"
description: "in the source code of android, we can also root from it"
category: "android"
tags: [android, gingerbread, linux, kernel, root]
---
{% include JB/setup %}

网上有各种编译Android代码和Android的Linux内核代码的文章，但是对于如何在编译了Linux进行root没有相应的说明，自己尝试进行了。   
经试验：2.3.3无法与该内核连接，没能找到原因，所以选用2.3.4，发现可以成功。   

假设Android source已下载到`~/Android/source/`   
然后把Android linux kernel下载到`~/Android/kernel/`   
下载地址，见官网[http://source.android.com/source/building-kernels.html](http://source.android.com/source/building-kernels.html),
使用里面`samsung.git`    
###make流程: (`pwd = ~/Android/kernel/samsung/`)
	
	export PATH=$PATH:~/Android/source/prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin/
	make ARCH=arm clean
	make ARCH=arm herring_defconfig
	make -j4 ARCH=arm CROSS_COMPILE=arm-eabi-
	
其中`~/Android/source/`和`~/Android/kernel/`是下载Android source和kernel后自定义的目录。   
第一步写在`./bashrc`里啥的都行。如果需要对内核进行配置，在第3步以后进行`make ARCH=arm menuconfig`，不需要的话这步不用考虑。   

###编译后的结果
kernel:   
~/Android/kernel/samsung/arch/arm/boot/zImage   
wifi module:   
~/Android/kernel/samsung/drivers/net/wireless/bcm4329/bcm4329.ko   
   
###制作boot.img流程
1. 首先从官方的ROM里把原来的`boot.img`搞出来（或者从刷好的手机里搞出来 `cat /dev/mtd/mtd2 > /sdcard/boot.img`）
2. 搞一坨工具放在自建的`NS-bootwork`文件夹里，Copy the boot.img from your Nexus S to your NS-bootwork folder
3. 然后进行一系列操作：（其中用到的工具主要是`unpack-bootimg.pl`和`mkbootimg`）
		
		cd ~/NS-bootwork
		./unpack-bootimg.pl boot.img
		cd boot.img-ramdisk/
		find . | cpio -o -H newc | gzip > ../ramdisk-repack.cpio.gz
		cd ..
		./mkbootimg --kernel zImage --ramdisk ~/Android/ROM/NS-bootwork/ramdisk-repack.cpio.gz --base 0x30000000 --pagesize 4096 -o newtestboot.img
		
这个`newtestboot.img`就是最后生成的`boot.img`，然后就是刷到手机上：   

4. 直接刷到手机上（手机先关机然后进入bootloader）: `fastboot flash boot newtestboot.img`，如果不写flash，表示只是这一次启动用这个内核，并没有写到写到手机里，下次启动用的还是原有的内核。
5. 制作成rom：这个没啥说的，直接替换掉rom里面的`boot.img`就可以，当然要改名成`boot.img`，然后rom压缩zip签名即可。

##刷内核后的几个问题
**版本问题**   
gingerbread的内核2.6.35.7应该是支持2.3.3的，但一直未成功，所以后来使用2.3.4。  
    
**网络模块问题**   
网上说编译后的bcm4329.ko应该上传到手机`/system/modules/bcm4329.ko`，刷好的手机上本来就有这个文件，且modules文件夹下只这一个文件，替换即可，但我替换后均不成功，后来找到一个勉强可用的方法，设置成静态IP，能上网了。估计是DHCP有点毛病，先不管了，能用就行。   
    
**刷内核后的adb root问题**   
1. adb push: 文件系统是只读的，不能写，只要`adb remount`就可以。   

2. 前人root的方法是使用superroot等工具，但是对于自己编译的内核没有效果，因为很多脚本相工具的实质是刷了有root的内核，但现在自己制作了新内核，之前的root方法好多就不灵了。
   
##Adb root的方法
先说最键的部分，得到adb的root只需要在`boot.img`里的某个文件改一个字符就可以了！   
即：`boot.img`中的`boot.img-ramdisk/default.prop`中第一行ro.secure=1改成0就可以了！   
   
原理如下（原理找起来就麻烦了，不了解也罢）
为了`adb shell`到手机的时候有root权限，必须让跑在手机端的服务进程adbd有root权限，看代码：
`~/Android/source/system/core/adb/adb.c`   这个在source的代码里，不是kernel
	
	 866     /* run adbd in secure mode if ro.secure is set and
	 867     ** we are not in the emulator
	 868     */
	 869     property_get("ro.kernel.qemu", value, "");
	 870     if (strcmp(value, "1") != 0) {
	 871         property_get("ro.secure", value, "");
	 872         if (strcmp(value, "1") == 0) {
	 873             // don't run as root if ro.secure is set...
	 874             secure = 1;
	 875 
	 876             // ... except we allow running as root in userdebug builds if the 
	 877             // service.adb.root property has been set by the "adb root" command
	 878             property_get("ro.debuggable", value, "");
	 879             if (strcmp(value, "1") == 0) {
	 880                 property_get("service.adb.root", value, "");
	 881                 if (strcmp(value, "1") == 0) {
	 882                     secure = 0;
	 883                 }
	 884             }
	 885         }
	 886     }
	
从中可以看出只有`ro.secure`设置了并且不是在emulator里跑的时候才会把权限降到root以下，所以把`ro.secure`小改即可，这也解释了为什么在emulator中adb默认就是root的问题了。   
   
google class的kernel building小组里有人说可以把`boot.img-ramdisk/init.rc`中的user shell一句注释掉，但我试验后发现不行。   
