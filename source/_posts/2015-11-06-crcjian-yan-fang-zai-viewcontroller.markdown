---
layout: post
title: "在.mm文件中调用一个C函数"
date: 2015-11-06 14:25:59 +0800
comments: true
categories: iOS C++
---

在项目中使用crc进行校验，在网上找到一份c的源码，但是放到项目当中一直编译失败，提示找不到对应的函数
一开始是认为是传递的参数不对，发现不是这个问题,`viewController.mm`中是用OC++编译的，导致编译不过，但是不明白原因，现在的解决办法是把这c函数放到`.m`的文件中编译就可以了。

---

原因分析：原因是由于`ViewController.mm`是`.mm`后缀的，会按照OC++编译,所以在`ViewController.mm` 调用`int add(int a,int b)`函数，在链接阶段,就会去目标文件中去找`_add_int_int`这样的符号,不同的编译器可能会不同，但是C函数编译后生成的符号是`_int`，这就导致找不到对应的函数，编译失败,解决办法在C头文件中添加`extern "C"`，即可。详细解释，开下面的连接博客。

> http://www.cnblogs.com/skynet/archive/2010/07/10/1774964.html
> http://www.jianshu.com/p/5d2eeeb93590

