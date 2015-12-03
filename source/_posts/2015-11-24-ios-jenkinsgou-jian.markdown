---
layout: post
title: "ios-jenkins构建"
date: 2015-11-24 10:12:57 +0800
comments: true
categories: 
---
## iOS SDK自动化构建

1. C库代码引用
    C库文件太多，而且里面有很多文件不能引用到项目中，不然会导致编译失败，以前使用的办法是先全部引入再删除部分文件，太麻烦了，想通过修改配置文件的方式
2. Framework的引用
3. Configure文件 设置buildSetting
4. 自动测试批量测试测试报告生成
5. MAC 小软件自动生成SDK Demo
6. jenkins的自动构建
7. jenkins 自动打包生成ipa上传测试
8. 自动生成Xcode工程，
 
 
 ----
 # 测试项目直接包含SDK工程
 
 1. `.mm`
 2. 引用`Framework` 这个可以后面通过修改xc配置文件完成
 3. `User Search Header` 这个可以通过`config` 配置文件完成。
