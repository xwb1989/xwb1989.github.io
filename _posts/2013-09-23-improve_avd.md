---
layout: post
title: 改善AVD的运行速度
category: blog
---
#改善AVD的运行速度

这两天在开始做COSI153 Mobile Application Development的作业。课程讲的是Android开发，需要使用Android Virtual Device。也许是因为我没有认真听课没有正确优化AVD的配置，总之我的MBA 2013上的「Nexus 4」运行速度慢得无法忍受。在Google和[STO](http://stackoverflow.com/questions/1554099/slow-android-emulator)的帮助下，找到了通过安装__Intel Hardware Accelerated Execution Manager（HAXM）__改善AVD的运行速度。

##安装HAXM

OS X
>http://software.intel.com/en-us/articles/installation-instructions-for-intel-hardware-accelerated-execution-manager-macosx

按照上方链接的指引即可在OS X中安装HAXM。

##安装Intel Atom x86 System Image
仅仅安装HAXM还不够。还需要在SDK中给相应的Android OS装上`Inter Atom x86 System Image`。

##配置基于Intel x86 CPU的AVD
在成功完成上述步骤后，就可以创建基于Intel x86 CPU的AVD。在新建AVD的CPU一栏中选择`Intel Atom x86`，然后再勾选`Use Host GPU`。需要注意的是，给AVD配置的内存需小于安装HAXM时所分配的内存，否则无法使用HAXM加速。如果HAXM配置的内存过小，重新安装即可。

