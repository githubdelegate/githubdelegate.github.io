---
layout: post
title: "crc的c函数放在ViewController.mm中编译失败问题分析"
date: 2015-11-06 14:25:59 +0800
comments: true
categories: iOS C++
---

在项目中使用crc进行校验，在网上找到一份c的源码，但是放到项目当中一直编译失败，提示找不到对应的函数
一开始是认为是传递的参数不对，发现不是这个问题,`viewController.mm`中是用OC++编译的，导致编译不过，但是不明白原因，现在的解决办法是把这c函数放到`.m`的文件中编译就可以了。

