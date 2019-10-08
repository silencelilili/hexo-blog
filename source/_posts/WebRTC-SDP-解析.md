---
title: WebRTC SDP 解析
date: 2019-09-30 17:21:44
tags: WebRTC
categories: WebRTC
---
#### SDP

**sdp（Session Description Protocol）**
是一种会话描述协议，属于文本协议，即WebRTC中常说的信令（Signalling），是WebRTC用来协助建立p2p通讯的。

主要用于协商双方通讯过程，传递基本信息，如：会话控制信息，用来开始和结束通话，即开始视频、结束视频这些操作指令；处理错误的消息；元数据，如各自的音视频解码方式、带宽；网络数据，对方的公网IP、端口、内网IP及端口。但是WebRTC的API并没有实现信令通信机制，所以使用者需要自己去实现。常见的信令交互图如下：

一旦信令服务建立好了,两个Peer之间建立了连接,理论上他们就可以进行点对点通讯了。


**以下是一个实际使用的SDP内容，即各个字段解析：**


```
v=0

//sdp的版本号，目前为0

o=- 4024479478678337639 2 IN IP4 127.0.0.1

//格式：o=<username> <sess-id> <sess-version> <nettype> <addrtype> <unicast-address>

//username没有用-表示，sess-id为会话id，本处为4024479478678337639， sess-version为会话版本，本处为2，后面依次为

//TCP通信/IP4协议/本地ip

s=-

//会话名字，没有用-

t=0 0

//会话开始/结束时间，0表示没有限制

a=group:BUNDLE audio video

//group为BUNDLE表示audio和video共用一个媒体传输通道

a=msid-semantic: WMS

//WMS是WebRTC Media Stream的简称，改行定义了客户端支持同时传

//输多个流，一个流可以包括多个track

----------------------------------------Stream Description部分--------------------------

-------------audio 部分---------------

m=audio 9 UDP/TLS/RTP/SAVPF 111 103 9 102 0 8 105 13 110 113 126

//本处定义了audio相关的约定，9表示音频使用9端口来传输，一般不使用，如果为0表示不传输音频数据

//UDP/TLS/RTP/SAVPF表示使用的传输协议，udp表示使用udp传输，使用TLS加密，使用RTP协议封包，

//SAVPF表示使用SRTCP//的反馈机制控制通信过程

//111 103 9 102 0 8 105 13 110 113 126表示该音频支持的编码，分别在下面的a=rtpmap处定义

c=IN IP4 0.0.0.0

//用来传输音频数据使用的ip地址，webrtc不使用该地址

a=rtcp:9 IN IP4 0.0.0.0

//用来传输rtcp的地址及端口号，webrtc中不使用

a=ice-ufrag:0uAB

a=ice-pwd:eeHkODiYv9t5qAZzT07XFzlO

//ice协商过程中使用的安全验证信息

a=ice-options:trickle

//使用trickle方式，sdp里面描述媒体信息和ice后选项的信息可以分开传输

a=fingerprint:sha-256 73:7D:6B:7C:68:F2:48:8E:66:93:EB:64:E7:D0:B7:14:30:71:47:00:A9:65:8B:AC:B7:84:E6:61:DF:22:11:F8

//dtls协商过程中需要的认证信息

a=setup:actpass

//表示客户端再dtls协商过程中，可以做客户端也可以做服务器

a=mid:audio

//音频标示

a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level

//在rtp头部中加入音量信息

a=recvonly

//单向通信，仅仅作为接收端

a=rtcp-mux

//rtp，rtcp使用同一个端口传输

a=rtpmap:111 opus/48000/2

a=rtcp-fb:111 transport-cc

a=fmtp:111 minptime=10;useinbandfec=1

//111表示opus编码的编号，opus编码/采样率/声道数

//Minptime=10代表最小包时长是10ms，useinbandfec=1表示使用opus编码内置的fec特性

a=rtpmap:103 ISAC/16000

a=rtpmap:9 G722/8000

a=rtpmap:102 ILBC/8000

a=rtpmap:0 PCMU/8000

a=rtpmap:8 PCMA/8000

a=rtpmap:105 CN/16000

a=rtpmap:13 CN/8000

a=rtpmap:110 telephone-event/48000

a=rtpmap:113 telephone-event/16000

a=rtpmap:126 telephone-event/8000

//以上为audio支持的编码类型/采样率

----------------video部分----------------------

m=video 9 UDP/TLS/RTP/SAVPF 96 97 98 99 100 101 127 125 104

c=IN IP4 0.0.0.0

a=rtcp:9 IN IP4 0.0.0.0

a=ice-ufrag:0uAB

a=ice-pwd:eeHkODiYv9t5qAZzT07XFzlO

a=ice-options:trickle

a=fingerprint:sha-256 73:7D:6B:7C:68:F2:48:8E:66:93:EB:64:E7:D0:B7:14:30:71:47:00:A9:65:8B:AC:B7:84:E6:61:DF:22:11:F8

a=setup:actpass

a=mid:video

a=extmap:2 urn:ietf:params:rtp-hdrext:toffset

a=extmap:3 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time

a=extmap:4 urn:3gpp:video-orientation

a=extmap:5 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01

a=extmap:6 http://www.webrtc.org/experiments/rtp-hdrext/playout-delay

a=extmap:7 http://www.webrtc.org/experiments/rtp-hdrext/video-content-type

a=extmap:8 http://www.webrtc.org/experiments/rtp-hdrext/video-timing

a=recvonly

a=rtcp-mux

a=rtcp-rsize

a=rtpmap:96 VP8/90000

a=rtcp-fb:96 goog-remb

//支持使用rtcp包来控制发送方码流

a=rtcp-fb:96 transport-cc

a=rtcp-fb:96 ccm fir

//ccm是codec control using RTCP feedback message简称，意思是支//持使用rtcp反馈机制来实现编码控制，

//fir是Full Intra Request 简称，意思是接收方通知发送方发送完全帧过来

a=rtcp-fb:96 nack

//支持丢包重传

a=rtcp-fb:96 nack pli

//支持关键帧丢包重传

a=rtpmap:97 rtx/90000

a=fmtp:97 apt=96

a=rtpmap:98 VP9/90000

a=rtcp-fb:98 goog-remb

a=rtcp-fb:98 transport-cc

a=rtcp-fb:98 ccm fir

a=rtcp-fb:98 nack

a=rtcp-fb:98 nack pli

a=rtpmap:99 rtx/90000

a=fmtp:99 apt=98

a=rtpmap:100 H264/90000

a=rtcp-fb:100 goog-remb

a=rtcp-fb:100 transport-cc

a=rtcp-fb:100 ccm fir

a=rtcp-fb:100 nack

a=rtcp-fb:100 nack pli

a=fmtp:100 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=42e01f

//h264编码可选的附加说明

a=rtpmap:101 rtx/90000

a=fmtp:101 apt=100

a=rtpmap:127 red/90000

a=rtpmap:125 rtx/90000

a=fmtp:125 apt=127

a=rtpmap:104 ulpfec/90000
```
