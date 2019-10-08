---
title: getUserMedia() 出现的常见错误
date: 2019-09-30 17:31:15
tags: WebRTC
categories: WebRTC
---

遇到的错误：

```
 1. 用户没有摄像头，只有一个麦克风

 2. 用户（不小心地）拒绝了浏览器的使用摄像头请求

 3. 用户在你的getUserMedia()代码已经初始化之后才将摄像头/麦克风插到电脑上

 4. 媒体设备已经被其他的应用所占用了

 5. 只针对Firefox：设备已经被Firefox浏览器的其他标签页所占用了
```


###### getUserMedia()的媒体捕捉和流规范中定义了几种你可以在代码中处理的错误。

#### 1、NotFoundError

> 当你通过约束请求一个视频轨道但是用户没有摄像头的时候，这个错误就会出现。还有，当你请求一个音频/麦克风轨道的时候，但是电脑/设备并没有声卡或者录音设备被系统禁用的时候也会出现这个错误。但是这种情况比较罕见

#### 2、 NotReadableError

> 针对windows系统，当摄像头或者麦克风被占用的时候。在Windows上这个错误很常见，因为进程可以独占摄像头的访问权。除了Firefox，它不会在macOS上出现，因为mac系统允许几个进程共享摄像头/麦克风的使用权限。
>
> 在Windows系统上，Firefox会在其他应用或者Firefox标签页正在使用摄像头或者麦克风的时候弹出这个错误。这个错误类型是MediaStreamError，名称属性被设置为“NotReadableError”，消息属性被设置为“未能分配视频源”。
>
> Windows系统上的Chrome浏览器会弹出一个NavigarUserMediaError，其名称属性被设为“TrackStartError”，非规范的Chrome特定版本，没有消息提示。不同的Chrome标签页可以共享同一个摄像头。火狐也可以。
>
> 在mac系统上，这个错误只有在Firefox不止一个标签页尝试获取摄像头和麦克风的时候出现。会提示一个消息“当前麦克风进程受限”。
>

#### 3、OverconstrainedError

> 当你请求一个无法用硬件满足的约束时会出现在这个错误，举个例子，当使用min或者exact关键词请求一个比较高的帧速率或者高的分辨率的时候就会出现此错误。
>
> Firefox会提出一个MediaStreamError，其名称属性被设为“OverconstrainedError”。
>
> Chrome会弹出一个NavigatorUserMediaError，其名称属性设为“ConstraintNotSatisfiedErrror”，一个非规范的Chrome特定版本。
>
> Chrome和Firefox会返回请求的分辨率，或者当使用了ideal值的时候返回一个最接近的分辨率，但是如果你开始使用的是min关键字并赋予了比较大的值，或者exact关键字含有不支持的值，你就会立即触发这个错误。
>
> 错误项还会通过constraintName属性提醒你约束无法满足，并且会弹出消息“约束无法满足”。
>

#### 4、NotAllowedError

> 当用户拒绝（或者之前拒绝过）摄像头或者麦克风的使用请求时就会出现这个错误。
>
> Firefox会出现MediaStreamError，名称属性设置为“NotAllowedError”，以及弹出消息“用户代理或者当前平台不允许该请求”。
>
> Chrome会出现NavigatorUserMediaError，其名称属性设为“PermissionDeniedError”。
>

#### 5、TypeError

> 当传递给getUserMedia()的约束对象为空或者将所有轨道（音轨，视频轨，或者两者）被设置为false的时候就会出现这个问题。
>
> Firefox会提出MediaStreamError，其名称属性设置为“TypeError”，以及一个消息“音频和/或视频被请求”。
>
> Chrome会出现一条“TypeError： 无法在‘MediaDevices’上执行‘getUserMedia’：必须至少请求一个音频和视频”。
