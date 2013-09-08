---
layout: post
title: Java的文件读写基础
category: blog
---

#Java的文件读写基础

今天因为要写一个模仿cp的小程序（就叫作Copy吧），所以学习了下Java的文件读写机制。虽然是个非常简单的东西，不过自己还是弄了很久，感到非常挫败。由于自己才是初学（Java和CS都是），所以这篇笔记肯定会有很多错误，既是总结，也是对自己学习程度的记录吧。

##读写的种类

从读写的数据类型来看，Java的文件读写共分两类：一种是字符(Character)，另一种是字节(Byte)。前者要用到FileReader, FileWriter之类的字符处理接口，而后者要用到FileInputStream和FileOutputStream这样的字节流接口。

由于Copy不单要处理字符文件，所以要使用字节流接口。

##读写的过程

无论是字符流还是字节流，既然涉及到文件的读写，就会碰到至少两大问题：

* 文件是否存在
* 是否有相应的读写权限

虽然FileInputStream和FileOutputStream都支持`String file_path`作为参数来构建对象，但考虑到以上两个问题的存在，我还是先构建两个`File`对象`f1`和`f2`。假如`f2`已经存在，则询问是否覆盖。如果不存在，则创建`f2`。由于Java的exception机制，假如`f1`不存在，Java会抛出异常，因此不需要作特殊处理。

解决了`File`对象的问题之后，就以`f1`和`f2`为参数，构建`inputStream`和`outputStream`，并进行读写。

##Exception

在Java中，当涉及到文件的读写问题是，会强制要求在该`method`的`header`中加入异常处理。由于Copy同时涉及到读写，因此`FileNotFoundException`和`IOException`这两个`Exception`都需要加入。当读写操作遇到上述的两个问题是，会自动抛出异常，并提供相关的信息。

##命令行交互

当碰到目标文件已经存在时，Copy会询问是否覆盖以存在的文件，因此会需要命令行交互。一开始我尝试使用`Scanner`来实现这个功能，发觉并不合适（功能完全不对）。命令行交互应该使用`Console`来实现。

{% highlight java %}
import java.io.*;     //for Console
Console cons = System.console;        
{% endhighlight %}    
当需要与命令行交互时，只需要:

{% highlight java %}
String next;
next = cons.readLine();
{% endhighlight %}
即可。

需要注意的是，由于`next`是`String`，因此用`==`来判断`next`的值是不行的。需要用`String.equals(String another_string)`。

##总结
尽管是个非常非常非常简单的程序，不过已经让我碰到了很多麻烦。总结问题如下：

* 对Java的读写流程、机制不熟悉
    * 没弄清楚character跟byte stream的区别
    * 没弄懂Java读写接口的原理
* 没弄清楚命令行交互的方法
* 没弄清楚Java的Exception机制
    










