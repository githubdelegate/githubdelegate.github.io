---
layout: post
title: "iOS8 framework动态库遇到的问题"
date: 2015-09-08 14:32:29 +0800
comments: true
categories: "iOS"
---

1. 编译显示`Include of non-modular header inside framework module` 错误
 
 	 修改`Build Setting` 中`Allow Non-modular Include InFramework Modules` 为YES.
 
 
2. 在代码中引用头文件 `#import "***Kit.h"` 提示找不到
			
	这个时候要检查对应的framework target `Build Phases->Headers->Public` 是否包含对应的类。一般要提供给其他target使用的类的.h文件都应该放到Public中.
 