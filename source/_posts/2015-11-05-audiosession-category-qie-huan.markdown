---
layout: post
title: "AudioSession Category 设置扬声器播放"
date: 2015-11-05 10:13:15 +0800
comments: true
categories: "AudioSession"
- AudioSession
published: false
---

在项目中遇到的问题是，使用`AudioQueue` 录音，进行语音识别，识别结束后，播报识别结果，但是录音还要继续，遇到的问题是播放的时候声音很小。原因是由于录音的时候设置了 

	[[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryPlayAndRecord error:nil];



这样设置之后，默认播放rout是听筒，而不是扬声器，声音就小了。
修改方法如下
方法一：
        
    [[AVAudioSession sharedInstance] setCategory:AVAudioSessionCategoryPlayAndRecord withOptions:AVAudioSessionCategoryOptionDefaultToSpeaker | AVAudioSessionCategoryOptionAllowBluetooth error:nil];
    
方法二：

	[session overrideOutputAudioPort:AVAudioSessionPortOverrideSpeaker error:&error];

这两个方法的区别可以看这里[AVAudioSessionCategoryOptionDefaultToSpeaker和AVAudioSessionPortOverrideSpeaker区别](https://developer.apple.com/library/ios/qa/qa1754/_index.html)，大概意思就是设置`AVAudioSessionPortOverrideSpeaker` 是临时性的，如果rout改变和audioSession被中断的话，这个设置就失效了，会返回到默认的设置。设置`AVAudioSessionCategoryOptionDefaultToSpeaker` 后，会一直生效。






