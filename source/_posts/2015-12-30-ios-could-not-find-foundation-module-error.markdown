---
layout: post
title: "iOS could not find Foundation module error"
date: 2015-12-30 14:41:25 +0800
comments: true
categories: 
---
# iOS Xcode 编译遇到`could not find Foudation module` 错误

在pch 文件中引用 `#import <Foundation/Foundation.h>` 出错
解决办法：添加`#ifdef __OBJC__ 在这里引用 #endif`
