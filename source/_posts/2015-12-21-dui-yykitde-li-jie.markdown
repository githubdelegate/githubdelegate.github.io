---
layout: post
title: "对YYKit的理解"
date: 2015-12-21 16:38:04 +0800
comments: true
categories: 
---


### 1.YYKit Foudation中的`NSObject+YYAdd`分类
* C中的可变参数
> http://blog.csdn.net/edonlii/article/details/8497704
> [深入C语言可变参数(va_arg,va_list,va_start,va_end,_INTSIZEOF)
](http://www.cnblogs.com/haoyuanyuan/p/3221463.html)

1.`performSelectorWithArgs` 方法：中用到了`NSInvocation`and`NSMethodSignature`。
NSInvocation 可以理解成一个发送一个消息，并接收消息返回内容，执行invoke就传递消息，里面包含参数，接受者，返回值。

NSMethodSignature 是一个方法的封装，包括方法的参数类型，个数，返回值类型，返回值长度。

2.`+ (BOOL)swizzleInstanceMethod:(SEL)originalSel with:(SEL)newSel ` 方法。用到了runtime的中交换两个方法的实现。

> [ Objective-C的hook方案（一）: Method Swizzling](http://blog.csdn.net/yiyaaixuexi/article/details/9374411#t1)

3. `- (void)addObserverBlockForKeyPath:(NSString*)keyPath block:(void (^)(__weak id obj, id oldVal, id newVal))block;` KVO 直接block不使用代理模式，简便明朗。
KVO实现原理： 当某个类的对象第一次被观察时，系统就会在运行期动态地创建该类的一个派生类，在这个派生类中重写基类中任何被观察属性的 setter 方法。
派生类在被重写的 setter 方法实现真正的通知机制，就如前面手动实现键值观察那样。这么做是基于设置属性会调用 setter 方法，而通过重写就获得了 KVO 需要的通知机制。当然前提是要通过遵循 KVO 的属性设置方式来变更属性值，如果仅是直接修改属性对应的成员变量，是无法实现 KVO 的。

>[KVC/KVO原理详解及编程指南](http://blog.csdn.net/wzzvictory/article/details/9674431)

>[Objective-C对象之类对象和元类对象（一）](http://blog.csdn.net/wzzvictory/article/details/8592492)

>[Objective-C isa 指针 与 runtime 机制](http://www.jianshu.com/p/41735c66dccb)

4.`__weak` 和`__block`的区别
	
____weak 本身是可以避免循环引用的问题的，但是其会导致外部对象释放了之后，block 内部也访问不到这个对象的问题，我们可以通过在 block 内部声明一个 __strong 的变量来指向 weakObj，使外部对象既能在 block 内部保持住，又能避免循环引用的问题

____block 本身无法避免循环引用的问题，但是我们可以通过在 block 内部手动把 blockObj 赋值为 nil 的方式来避免循环引用的问题。另外一点就是 __block 修饰的变量在 block 内外都是唯一的，要注意这个特性可能带来的隐患。

[__weak与__block区别](http://honglu.me/2015/01/06/weak与block区别/)


