---
layout: post
title: "iOS使用AudioQueue录音遇到的内存泄露问题"
date: 2015-08-04 00:15:17 +0800
comments: true
categories: 
keywords: Octopress github 搭建博客 添加统计 SEO 录入搜索引擎
description: 为Octopress博客录入搜索引擎，并添加流量统计信息，进行SEO。
---


### AudioQueue 录音
`AudioQueue` 是iOS中比较底层的音频处理类，可以用来播放和录音。录音的话，每个一段时间都会通过一个C的回掉函数返回录音数据。如下面的代码：

```
// 回调函数
void inputBufferHandler(void *inUserData, AudioQueueRef inAQ, AudioQueueBufferRef inBuffer, const AudioTimeStamp *inStartTime,
                        UInt32 inNumPackets, const AudioStreamPacketDescription *inPacketDesc)
{
    Recorder *recorder = (__bridge Recorder *)inUserData;
    if (recorder == nil){
        return;
    }
    
    if ((inNumPackets > 0) && (recorder.isRecording))
    {
         // 获取录音数据
        int _pcmSize = inBuffer->mAudioDataByteSize;
        char *_pcmData = (char *)inBuffer->mAudioData;
		
        NSData *data = [[NSData alloc] initWithBytes:_pcmData length:_pcmSize];
        // 把录音数据传递出去
        [recorder writeRecordingData:data];
    }
}
```
-----

上面这段代码有问题，如果一直不停的录音，会发现内存一直不停的增加，而且每次增加的大小会和每次返回的录音数据大小相等。
那导致这段代码内存泄露的原因，就是`NSData *data = [[NSData alloc] initWithBytes:_pcmData length:_pcmSize];` 这段代码中` data` 没有释放导致的。虽然是在ARC环境下，但是这是个C函数，要自己管理内存，所以加上`@autorelease` 就能解决。

----
> 引用
> [Memory Management with Objective C / Cocoa / iPhone ](http://memo.tv/archive/memory_management_with_objective_c_cocoa_iphone)
 









