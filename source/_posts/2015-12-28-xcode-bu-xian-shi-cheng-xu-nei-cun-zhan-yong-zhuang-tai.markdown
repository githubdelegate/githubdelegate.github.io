---
layout: post
title: "Xcode 不显示程序内存占用状态"
date: 2015-12-28 15:57:23 +0800
comments: true
categories: 
---

### Xcode 突然不显示程序运行的内存占用信息了

办法：`Product`-> `Scheme`->`Edit Scheme` -> `Diagnostic`-> `取消Enable Zombie Objects` 选项。