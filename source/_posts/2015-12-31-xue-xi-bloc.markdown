---
layout: post
title: "学习block"
date: 2015-12-31 10:24:12 +0800
comments: true
categories: 
---

> [block没那么难](https://www.zybuluo.com/MicroCai/note/57603)

## 1.block 基本构成



### 2.`__block` 和 `__weak` 区别


3. __block 在 ARC 和非 ARC 下含义一样吗？
__block 在 ARC 下捕获的变量会被 block retain, 这样可能导致循环引用, 所以必须要使用弱引用才能解决该问题. 而在非 ARC 下, 可以直接使用 __block 说明符修饰变量, 因为在非 ARC 下, block 不会 retain 捕获的变量.


2. 以下 keywords 有什么区别: assign vs weak, __block vs __weak
assign 和 weak 是用于在声明属性时, 为属性指定内存管理的语义.

assign 用于简单的赋值, 不改变属性的引用计数, 用于 Objective-C 中的 NSInteger, CGFloat 以及 C 语言中 int, float, double 等数据类型.
weak 用于对象类型, 由于 weak 同样不改变对象的引用计数且不持有对象实例, 当该对象废弃时, 该弱引用自动失效并且被赋值为 nil, 所以它可以用于避免两个强引用产生的循环引用导致内存无法释放的问题.
__block 和 __weak 之间的却是确实极大的, 不过它们都用于修饰变量.

前者用于指明当前声明的变量在被 block 捕获之后, 可以在 block 中改变变量的值. 因为在 block 声明的同时会截获该 block 所使用的全部自动变量的值, 而这些值只在 block 中只具有"使用权"而不具有"修改权". 而 __block 说明符就为 block 提供了变量的修改权.
后者是所有权修饰符, 什么是所有权修饰符? 这里涉及到另一个问题, 因为在 ARC 有效时, id 类型和对象类型同 C 语言中的其他类型不同, 必须附加所有权修饰符. 所有权修饰符一种有 4 种:
__strong
__weak
__unsafe_unretained
__autorelease
__weak 与 weak 的区别只在于, 前者用于变量的声明, 而后者用于属性的声明.

