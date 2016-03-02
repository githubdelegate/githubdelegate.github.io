---
layout: post
title: "iOS相关证书全部失效"
date: 2016-03-02 17:14:33 +0800
comments: true
categories: 
---

某天打开电脑突然发现iOS相关证书全部失效。全部显示`证书的签发者无效`。

先找到一个失效证书，右击->评估证书->代码签名->显示证书，可以看到证书失效的原因：
![图片](./certificate-invalidate.png)


去这个地址下载最新证书：`https://developer.apple.com/certificationauthority/AppleWWDRCA.cer` 安装.
同时要把老证书全部删掉，在keychains里选择login,然后点选Certificates，在这个界面，选择工具栏的View -> Show Expired Certificates，这时候你会发现一个过期的“WWDR Certificate”（Apple Worldwide Developer Relations Certification Authority），删除它，同样的办法，在System的那一栏也有这个过期的“WWDR Certificate”.
如果过期证书全部删除，并且已安装新证书，会发现证书全部都OK了。
