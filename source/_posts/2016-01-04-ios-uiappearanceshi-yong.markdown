---
layout: post
title: "iOS UIAppearance使用"
date: 2016-01-04 11:06:33 +0800
comments: true
categories: "UIAppearance"
---

# iOS UIAppearance使用

### 为什么要使用 `UIAppearance`

在iOS 5以前，自定义原生控件的外观并没有原生支持，因此开发人员感觉很麻烦。开发人员经常面临的问题是修改一个控件所有实例的外观。解决这个问题的正确方法是重写一遍控件。但由于这么做非常费时，一些开发人员开始覆盖或混写一些方法，如drawRect:。

从iOS 5开始，苹果通过两个协议（UIAppearance和UIAppearanceContainer）规范了对许多UIKit控件定制的支持。所有遵循UIAppearance协议的UI控件通过定制都可以呈现各种外观。不仅如此，UIAppearance协议甚至允许开发者基于控件所属的区域指定不同的外观。也就是说，当某个控件包含在特定视图中时，可以指定它的外观（如UIBarButtonItem的tintColor）。也可以获取该控件类的外观代理对象，用该代理定制外观来实现，下面来看一个例子

iOS5及其以后提供了一个比较强大的工具UIAppearance，我们通过UIAppearance设置一些UI的全局效果，这样就可以很方便的实现UI的自定义效果又能最简单的实现统一界面风格，它提供如下两个方法。

`+ (id)appearance`
这个方法是统一全部改，比如你设置UINavBar的tintColor，你可以这样写：[[UINavigationBar appearance] setTintColor:myColor];

`+ (id)appearanceWhenContainedIn:(Class <>)ContainerClass,...`
这个方法可设置某个类的改变：例如：设置UIBarButtonItem 在UINavigationBar、UIPopoverController、UITabbar中的效果。就可以这样写
[[UIBarButtonItem appearanceWhenContainedIn:[UINavigationBar class], [UIPopoverController class],[UITabbar class] nil] setTintColor:myPopoverNavBarColor];


	请注意＊使用appearance设置UI效果最好采用全局的设置，在所有界面初始化前开始设置，否则可能失效。

但是它并不是支持所有的UI类。下面列出它支持的类

　　1.UIActivitiIndicatorView

　　2.UIBarButtonItem

　　3.UIBarItem

　　4.UINavgationBar

　　5.UIPopoverControll

　　6.UIProgressView

　　7.UISearchBar

　　8.UISegmentControll 

　　9.UISlider

　　10.UISwitch

　　11.UITabBar

　　12.UITabBarItem

　　13.UIToolBar

　　14.UIView

　　15.UIViewController
　　
### 自己创建一个可自定义外观的控件

对于我们自己定义的控件，也可以支持UIAppearance协议，这样我们的控件也能支持自定义了。你只需要写一个设置外观的settor，然后在settor方法后面加上“UI_APPEARANCE_SELECTOR”标记就可以。

### UIAppearance实现原理

在通过UIAppearance调用“UI_APPEARANCE_SELECTOR”标记的方法来配置外观时，UIAppearance实际上没有进行任何实际调用，而是把这个调用保存起来（在Objc中可以用NSInvocation对象来保存一个调用）。当实际的对象显示之前（添加到窗口上，drawRect:之前），就会对这个对象调用之前保存的调用。当这个setter调用后，你的界面风格自定义就完成了。
