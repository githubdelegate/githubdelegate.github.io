---
layout: post
title: "iOS 多线程GCD和NSOperation"
date: 2015-12-28 20:30:35 +0800
comments: true
categories: 
---

### GCD和NSOperation使用说明

1.`NSOperation`
 
 1.NSOperation 类是一个抽象类，用来概括一个单独的任务的代码和数据。因为它是抽象类，所以要用他的子类，或者系统定义的子类（`NSInvocationOperation` && `NSBlockOperation`）去执行真正的任务。尽管是抽象类，但是`NSOperation`的实现包括了重要的逻辑可以用来协调任务的安全执行。这种内建的逻辑可以让你集中精力在任务的实现上，而不是为了保证任务的正确运行而写的各种控制粘合代码。
 一个operaton对象是一个单发对象，意味着它执行任务一次完成后，就不能再执行了。你必须把operations对象添加到operation queue，才能执行它们。一个operation queue执行它的operations即可以通过在子线程中直接执行，也可以间接的使用GCD.
如果你不想使用operation queue，你也可以通过直接调用`start`方法。因为start 一个不是在ready状态的operation，会导致exception，所以手动的执行operation会给代码带来负担。`ready`属性依据operation的准备就绪状态。


1.1 `Operation Dependencies`

依赖是一个很方便的方法来让operation按照某种顺序执行。你可以通过`addDependency:` 和 `removeDependency:`方法来添加或者删除依赖。默认，一个有依赖的operation ，不能进入准备就绪状态，直到它的所有的依赖的operation执行完。一旦依赖的最后一个Operation 执行完毕，它就进入ready并且可以执行了。
NSOperation的依赖之间并没有区别，不管一个被依赖的operation是成功的执行完成，还是不成功的，（换就话说就是，取消一个operation也是表示一个operation结束。）由你决定一个有依赖的operation是不是继续，当它的依赖operation被取消了，或者没有成功的执行完成。这就要求你在你的operation里面加上额外的错误跟踪机制。

1.2 `KVO-Compliant Properties`

`NSOperation` 类的属性可以使用KVC&KVO.
isCancelled - read-only
isAsynchronous - read-only
isExecuting - read-only
isFinished - read-only
isReady - read-only
dependencies - read-only
queuePriority - readable and writable
completionBlock - readable and writable

尽管你可以给这些属性添加观察者，但是你不应该把这些属性绑定上和界面相关的。因为界面相关的内容必须在主线程中执行。但是一个operation可能在任何一个线程中执行，同理KVO的通知也有可能在任何线程中执行。
如果你自己实现前面的属性，你的实现一定要遵从KVC&KVO,如果你定义了额外的属性，推荐你也让这些属性遵循KVC&KVO。

1.3 `Multicore Considerations`

`NSOperation` 类是支持多核的。所以不需要添加lock也能安全的访问NSOperation对象。这种行为是必要的,因为一个操作通常运行在一个单独的线程的创建和监控。当你自定义了一个NSOperation的子类，你必须保证任何覆盖的昂达保持线程安全。如果你自定义了一些方法，比如自定义的数据访问，你必须保证线程安全，意味着任何的访问变量的操作都必须是同步的，防止潜在数据污染。

1.4 `Asynchronous Versus Synchronous Operations`

如果你计划执行一个operation手动的，你应该设计你的Operation以同步或者异步的方式执行，而不是把它添加到queue中。
Operation默认是同步的方式。在同步的方式中，operation不会创建一个新线程去执行，当你调用`start`方法的时候，operation在当前线程执行。当start方法返回的时候，任务执行完成。
当你调用一个异步的operation的start方法，start会在对应的任务完成之前返回。一个异步的operation对象有责任在子线程中安排它的任务。operation可以通过直接开启一个新线程，调用一个异步方法，或者使用GCD.如果operation正在执行，方法返回了，这并没有关系，只要operation还在进行中就行。

如果你只要考虑使用queue去执行你的operations。把它们定义成同步的会很简单。如果你要执行你的Operation手动的，所以你可能希望定义你的operation是异步的。定义一个异步的operation要求更多的工作量，因为你必须监控你的任务的运行状态，用KVO通知状态变化。但是定义一个异步的Operation很有用，当你手动执行operaiton并且不希望阻塞调用线程的时候。
当你添加一个operation到一个queue的时候，queue不在乎异步的属性，总是直接在异步线程调用start方法。所以如果你一直通过添加operation到queue的方法去执行operation，根本没有必要让operation是异步的。


1.5 `Subclassing Notes`

`NSOPeration`类提供了基本的逻辑来追踪执行状态，但是必须继承它才能干实际的工作。怎么创建你的子类，依赖于你
怎么设计你的operation，是并发的还是不并发的。
  
  * 对于不并发的opeartion，你只需要重写一个`main`方法：在这个方法中，放置任务需要执行的代码。你应该自己定义一个初始化方法，让创建实例更简单。也可以定义`getter` `setter`方法，你必须保证这些方法是多线程安全的。

  * 对于并发的operation，至少需要重写`start`,`asynchronous`,`executing`,`finished`方法。在并发操作中，你的start方法要开启operation异步地，不论是创建一个新线程还是调用异步的方法。一旦开启了operation，你的start方法还要更新executing属性状态，发送KVO通知。一旦执行完成或者取消执行了，你的同步的operation对象必须发送KVO通知，对应的`isExecuting` 和`isFInished`属性。(如果是取消，更新isFinisheh很重要，尽管Operation没有完整的完成任务)。

2.`NSInvocationOperation`

`start`方法：这个方法的默认实现是更新operation执行状态，调用`main`方法。当然这个方法也会检查确保operation可以run。例如，如果cancelled，或者finished，就不会调用main方法直接返回。如果operation当前正在运行或者还没准备好运行，会抛出`NSInvalidArgumentException`异常。
注意:如果一个operation依赖一些还没finished的operations，那么就认为当前operation还没进入ready状态。
如果你是实现一个同步operation，你必须重写start方法，在start方法里面初始化operation。你自己的start方法不能调用父类的start方法。为了设置运行环境，你的实现必须根据情况改变运行的状态。当operaton开始执行，然后结束运行的时候，要发送KVO通知，`isExecting` 和`isFinished`。
如果你想主动主动的执行你的operations，你可以直接调用`start`方法。然而，对一个已经添加到queue的operation调用start方法是错误的。把一个已经调用过start方法的operation添加到queue，也是错误的。一旦把一个operation添加到queue，queue就会对这个operation全权负责了，你不需要关心了。


#### `NSBlockOperation&NSInvocationOperation`

`NSBlockOperation` 管理多个block的同步执行。你可以用这个对象一次执行多个block而不用分开创建不同的operation对象。当执行多个block的时候，只有当全部的block finished才算这个operation finished。


``` 
   
       NSBlockOperation *blockOperation = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"1th block--current thread:%@",[NSThread currentThread]);
    }];
    
    // operation的所以block执行完成后，才会执行。在当前线程中执行。
    blockOperation.completionBlock = ^(){
        NSLog(@"blockoperaton 完成啦啦啦啦啦---current thread:%@",[NSThread currentThread]);
    };
    
    [blockOperation addExecutionBlock:^{
        NSLog(@"2th block--current thread:%@",[NSThread currentThread]);
    }];
    
    [blockOperation addExecutionBlock:^{
        NSLog(@"add 3th block--current thread:%@",[NSThread currentThread]);
    }];
    
    // 1.手动start执行，第一个block会在当前线程中执行，其余block会在异步线程中
    // [blockOperation start];
    
    // 2.放到queue中执行，queue会把其中的operation放在异步线程中执行，所以block会全部在异步线程中执行。并且是并发执行。
    [[[NSOperationQueue alloc] init] addOperation:blockOperation];
    
//##---------------------------------------------
    NSInvocationOperation *invocationOperation = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run:) object:@"123"];
    invocationOperation.completionBlock = ^(){
        NSLog(@"invocation完成--current thread:%@",[NSThread currentThread]);
    };
    
    // 1.手动start执行.在当前线程中执行。
    // [invocationOperation start];
    
    // 2.放到queue中执行，queue会把其中的operation放在异步线程中执行.
    [[[NSOperationQueue alloc] init] addOperation:invocationOperation];

```

输出结果：

```
2015-12-29 15:15:29.222 NSOperationTest[62550:4394450] add 3th block--current thread:<NSThread: 0x796677a0>{number = 4, name = (null)}
2015-12-29 15:15:29.222 NSOperationTest[62550:4394449] 2th block--current thread:<NSThread: 0x79667970>{number = 5, name = (null)}
2015-12-29 15:15:29.222 NSOperationTest[62550:4394436] 1th block--current thread:<NSThread: 0x79667670>{number = 3, name = (null)}
2015-12-29 15:15:29.222 NSOperationTest[62550:4394453] invocationOperaton---current thread:<NSThread: 0x7978c4d0>{number = 6, name = (null)}--obj=123
2015-12-29 15:15:29.225 NSOperationTest[62550:4394449] invocation完成--current thread:<NSThread: 0x79667970>{number = 5, name = (null)}
2015-12-29 15:15:29.225 NSOperationTest[62550:4394453] blockoperaton 完成啦啦啦啦啦---current thread:<NSThread: 0x7978c4d0>{number = 6, name = (null)}

```

结论：`NSBlockOperation`添加的block会添加到`executionBlocks`中，猜测`start`方法的大概伪代码实现是

```
- (void)start
{
    Block block  = executionBlocks.firstObj;
    block(); // 先执行第一个block

    for (int i = 1; i<executionBlocks.count; i++) {
        dispatch_async(dispatch_get_global_queue(0, 0), ^{
            (block)executionBlocks[i](); // 异步执行剩下的block
        });
    }
    
    // 当前面的任务全部完成后调用完成block
    self.completionBlock();
}

```

当放入queue中时，就是再异步线程中调用当前operation的 `start`方法。



2.`GCD`
 
