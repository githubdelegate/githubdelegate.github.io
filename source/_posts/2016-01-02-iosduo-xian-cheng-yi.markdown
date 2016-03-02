---
layout: post
title: "iOS多线程一"
date: 2016-01-02 15:50:05 +0800
comments: true
categories: 
---

## 1.3多线程方案

技术 | 描述 | 
------------ | ------------- | ------------
Operation objects | 一个operation对象是一个任务的包装，可以在子线程中正常的执行。这层包装隐藏了线程的管理，让你专心任务本身。你要把它和operation queue相结合。operation queue可以在多个线程中管理operation对象。  | Content Cell
GCD | GCD 是另一种使用多线程的方式，能让你专注于你的任务而不用担心线程管理。使用GCD,你设计你的任务，把任务添加到一个queue，queue负责调度你的任务在合适的线程。queue会计算当前可用的资源，让你的任务更高效的执行。  | Content Cell
Idle-time notifications| 对于一些很短，优先级很低的任务，空闲时间通知会在你的程序空闲的时候执行任务。Cocoa 用`NSNotificationQueue`提供了支持。 ||
Asynchronous functions|系统接口提供了很多异步函数可以自动同步。这些API使用系统用户进程或者创建线程去执行任务，并把结果给你。||
Timers|你可以使用timers 在主线程中去执行定期的任务，这些任务||


### 1.3.1 线程包装
在应用层，全部的线程本质上都是一样的。开启一个线程后，线程运行状态有三种：`running`,`ready`,`blocked`。当一个线程当前不是运行，那么它可能是blocked，等待输入，或者是ready，等待调度。线程一直在这些状态中交替，直到退出，进入结束状态。
当你创建一个线程，你必须明确一个入口函数，入口函数构建在线程运行的代码。当函数返回或者你明确的结束线程，线程永远的停止并且会被系统回收。因为线程很消耗内存和时间，所以推荐你的入口函数处理大量的工作，或者创建一个`run loop`去做一些周期重复的工作。

### 1.3.2 Run Loops
 
 一个run loop就是用来在线程中管理异步到达的事件的一种机制。一个runloop工作原理就是监控一个或多个事件源。当事件到达，系统唤醒线程，调度事件到runloop，把事件调度给你设置的handlers。如果当前没有事件处理，runloop就把线程sleep。
并不要求你在线程中使用runloop，如果使用runloop可以给用户更好的体验。runloop可以保证线程使用最小的资源长期的存活下去，因为runloop会让线程进入睡眠状态，当线程无事可做的时候。它不需要不断的轮询，避免浪费CPU周期和防止处理器本身从睡觉和节约能源。
配置一个runloop，你不得不开启你的线程，获得一个你的线程的引用，安装你的事件handlers，然后告诉runloop run即可。OS X 的主线程已经配置好了runloop。如果你打算创建一个长期生存的线程，你必须为线程配置runloop。

### 1.3.3 同步工具
 多线程编程的一个最危险的就是多线程间的资源的争夺。如果多个线程尝试使用或修改同样的资源在同一时刻，问题就会出现。
Lock 提供一种狂野的方式保护一个时刻只能被一个线程执行的代码。最常见的锁是互斥锁，
系统还提供了条件变量conditions，用来保证程序中正确的任务执行顺序。一个条件就像门卫，阻塞线程直到条件为真。

### 1.3.4 线程间的交互

机制|简介|
---|----
Direct messaging|Cocoa支持直接在别的线程执行方法|
Global variables, shared memory, and objects||
Condition||
Run loop sources||

## 3.Run Loops

