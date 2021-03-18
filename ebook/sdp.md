# 描述语法
基于ABNF范式
[RFC5234/7405](https://www.rfc-editor.org/rfc/rfc7405.txt)
# sdp
1. sdp和http一样，是基于文本和键值对（key-value pair)的。
1. sdp 是个分层协议。先分了会话层和媒体层，媒体层里又分了好几部分。
1. sdp 标准（[RFC8866](https://tools.ietf.org/html/rfc8866)）只规定了有限制的东西，很多标准对其进行扩展,这是难点；标准在不断的更新，这也是需要注意的点。
1. sdp 各条目之间通过数字（payload type）相互引用。
## sdp示例
v=0  
//sdp版本号，一直为0,rfc4566规定  
o=- 7017624586836067756 2 IN IP4 127.0.0.1  
//origion/owner  o=<username> <session id> <version> <network type> <address type> <unicast-address>  
//username如何没有使用-代替，7017624586836067756是整个会话的编号，2代表会话版本，如果在会话  
//过程中有改变编码之类的操作，重新生成sdp时,sess-id不变，sess-version加1  
s=-  
//会话名,必选，没有的话使用-代替  
t=0 0  
//两个值分别是会话的起始时间和结束时间，这里都是0代表没有限制  
a=group:BUNDLE audio video data  
//需要共用一个传输通道传输的媒体，如果没有这一行，音视频，数据就会分别单独用一个udp端口来发送  
a=msid-semantic: WMS h1aZ20mbQB0GSsq0YxLfJmiYWE9CBfGch97C  
//WMS是WebRTC Media Stream简称，这一行定义了本客户端支持同时传输多个流，一个流可以包括多个track,  
//一般定义了这个，后面a=ssrc这一行就会有msid,mslabel等属性  
m=audio 9 UDP/TLS/RTP/SAVPF 111 103 104 9 0 8 106 105 13 126  
//m = <media><port><transport><fmt/payload type list>  
//m=audio说明本会话包含音频，9代表音频使用端口9来传输，但是在webrtc中一现在一般不使用，如果设置为0，代表不  
//传输音频,UDP/TLS/RTP/SAVPF是表示用户来传输音频支持的协议，udp，tls,rtp代表使用udp来传输rtp包，并使用tls加密  
//SAVPF代表使用srtcp的反馈机制来控制通信过程,后面111 103 104 9 0 8 106 105 13 126表示本会话音频支持的编码，后台几行会有详细补充说明  
c=IN IP4 0.0.0.0  
//表示你要用来接收或者发送音频使用的IP地址，webrtc使用ice传输，*不使用*这个地址  
a=rtcp:9 IN IP4 0.0.0.0  
//用来传输rtcp地地址和端口，webrtc中不使用  
a=ice-ufrag:khLS  
a=ice-pwd:cxLzteJaJBou3DspNaPsJhlQ  
//以上两行是ice协商过程中的安全验证信息  
a=fingerprint:sha-256 FA:14:42:3B:C7:97:1B:E8:AE:0C2:71:03:05:05:16:8F:B9:C7:98:E9:60:43:4B:5B:2C:28:EE:5C:8F3:17  
//是dtls协商过程中需要的认证信息  
a=setup:actpass  
//代表本客户端在dtls协商过程中，可以做客户端也可以做服务端，参考rfc4145 rfc4572  
a=mid:audio  
//在前面BUNDLE这一行中用到的媒体标识  
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level  
//指出我要在rtp头部中加入音量信息，参考 rfc6464  
a=sendrecv  
//指出我是双向通信，另外几种类型是recvonly,sendonly,inactive  
a=rtcp-mux  
//指出rtp,rtcp包使用同一个端口来传输  
//下面几行都是对m=audio这一行的媒体编码补充说明，指出了编码采用的编号，采样率，声道等  
a=rtpmap:111 opus/48000/2  
//可选 a=rtpmap:<fmt/payload type><encoding name>/<clock rate>[/<encodingparameters>]  
a=rtcp-fb:111 transport-cc  
//以上这行说明opus编码支持使用rtcp来控制拥塞，参考https://tools.ietf.org/html/draft-holmer-rmcat-transport-wide-cc-extensions-01  
a=fmtp:111 minptime=10;useinbandfec=1  
//可选 a=fmtp:<fmt/payload type> parameters  对rtpmap进一步说明  
//对opus编码可选的补充说明,minptime代表最小打包时长是10ms，useinbandfec=1代表使用opus编码内置fec特性  
a=rtpmap:103 ISAC/16000  
a=rtpmap:104 ISAC/32000  
a=rtpmap:9 G722/8000  
a=rtpmap:0 PCMU/8000  
a=rtpmap:8 PCMA/8000  
a=rtpmap:106 CN/32000  
a=rtpmap:105 CN/16000  
a=rtpmap:13 CN/8000  
a=rtpmap:126 telephone-event/8000  
a=ssrc:18509423 cname:sTjtznXLCNH7nbRw  
//cname用来标识一个数据源，ssrc当发生冲突时可能会发生变化，但是cname不会发生变化，也会出现在rtcp包中SDEC中，  
//用于音视频同步  
a=ssrc:18509423 msid:h1aZ20mbQB0GSsq0YxLfJmiYWE9CBfGch97C 15598a91-caf9-4fff-a28f-3082310b2b7a  
//以上这一行定义了ssrc和WebRTC中的MediaStream,AudioTrack之间的关系，msid后面第一个属性是stream-d,第二个是track-id  
a=ssrc:18509423 mslabel:h1aZ20mbQB0GSsq0YxLfJmiYWE9CBfGch97C  
a=ssrc:18509423 label:15598a91-caf9-4fff-a28f-3082310b2b7a  
m=video 9 UDP/TLS/RTP/SAVPF 100 101 107 116 117 96 97 99 98  
//参考上面m=audio,含义类似  
c=IN IP4 0.0.0.0  
a=rtcp:9 IN IP4 0.0.0.0  
a=ice-ufrag:khLS  
a=ice-pwd:cxLzteJaJBou3DspNaPsJhlQ  
a=fingerprint:sha-256 FA:14:42:3B:C7:97:1B:E8:AE:0C2:71:03:05:05:16:8F:B9:C7:98:E9:60:43:4B:5B:2C:28:EE:5C:8F3:17  
a=setup:actpass  
a=mid:video  
a=extmap:2 urn:ietf:params:rtp-hdrext:toffset  
a=extmap:3 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time  
a=extmap:4 urn:3gpp:video-orientation  
a=extmap:5 http://www.ietf.org/id/draft-hol ... de-cc-extensions-01  
a=extmap:6 http://www.webrtc.org/experiments/rtp-hdrext/playout-delay  
a=sendrecv  
a=rtcp-mux  
a=rtcp-rsize  
a=rtpmap:100 VP8/90000  
a=rtcp-fb:100 ccm fir  
//ccm是codec control using RTCP feedback message简称，意思是支持使用rtcp反馈机制来实现编码控制，fir是Full Intra Request  
//简称，意思是接收方通知发送方发送幅完全帧过来  
a=rtcp-fb:100 nack  
//支持丢包重传，参考rfc4585  
a=rtcp-fb:100 nack pli  
//支持关键帧丢包重传,参考rfc4585  
a=rtcp-fb:100 goog-remb  
//支持使用rtcp包来控制发送方的码流  
a=rtcp-fb:100 transport-cc  
//参考上面opus  
a=rtpmap:101 VP9/90000  
a=rtcp-fb:101 ccm fir  
a=rtcp-fb:101 nack  
a=rtcp-fb:101 nack pli  
a=rtcp-fb:101 goog-remb  
a=rtcp-fb:101 transport-cc  
a=rtpmap:107 H264/90000  
a=rtcp-fb:107 ccm fir  
a=rtcp-fb:107 nack  
a=rtcp-fb:107 nack pli  
a=rtcp-fb:107 goog-remb  
a=rtcp-fb:107 transport-cc  
a=fmtp:107 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=42e01f  
//h264编码可选的附加说明  
a=rtpmap:116 red/90000  
//fec冗余编码，一般如果sdp中有这一行的话，rtp头部负载类型就是116，否则就是各编码原生负责类型  
a=rtpmap:117 ulpfec/90000  
//支持ULP FEC，参考rfc5109  
a=rtpmap:96 rtx/90000    
a=fmtp:96 apt=100    
//以上两行是VP8编码的重传包rtp类型    
a=rtpmap:97 rtx/90000  
a=fmtp:97 apt=101  
a=rtpmap:99 rtx/90000  
a=fmtp:99 apt=107  
a=rtpmap:98 rtx/90000  
a=fmtp:98 apt=116
a=ssrc-group:FID 3463951252 1461041037  
//在webrtc中，重传包和正常包ssrc是不同的，上一行中前一个是正常rtp包的ssrc,后一个是重传包的ssrc  
a=ssrc:3463951252 cname:sTjtznXLCNH7nbRw  
a=ssrc:3463951252 msid:h1aZ20mbQB0GSsq0YxLfJmiYWE9CBfGch97C ead4b4e9-b650-4ed5-86f8-6f5f5806346d  
a=ssrc:3463951252 mslabel:h1aZ20mbQB0GSsq0YxLfJmiYWE9CBfGch97C  
a=ssrc:3463951252 label:ead4b4e9-b650-4ed5-86f8-6f5f5806346d  
a=ssrc:1461041037 cname:sTjtznXLCNH7nbRw  
a=ssrc:1461041037 msid:h1aZ20mbQB0GSsq0YxLfJmiYWE9CBfGch97C ead4b4e9-b650-4ed5-86f8-6f5f5806346d  
a=ssrc:1461041037 mslabel:h1aZ20mbQB0GSsq0YxLfJmiYWE9CBfGch97C   
a=ssrc:1461041037 label:ead4b4e9-b650-4ed5-86f8-6f5f5806346d  
m=application 9 DTLS/SCTP 5000  
c=IN IP4 0.0.0.0  
a=ice-ufrag:khLS  
a=ice-pwd:cxLzteJaJBou3DspNaPsJhlQ  
a=fingerprint:sha-256 FA:14:42:3B:C7:97:1B:E8:AE:0C2:71:03:05:05:16:8F:B9:C7:98:E9:60:43:4B:5B:2C:28:EE:5C:8F3:17  
a=setup:actpass  
a=mid:data  
a=sctpmap:5000 webrtc-datachannel 1024  
## webrtc 的answer 示例
{
    "type":"answer",
    "sdp":"v=0
o=- 5056162227500314557 1614928159 IN IP4 0.0.0.0
s=-
t=0 0
a=fingerprint:sha-256 55:CA:9F:BF:A0:A6:88:75:7D:71:80:EB:DF:08:89:94:A1:EB:4C:D7:96:EE:F5:5D:9F:CE:0C:47:4D:E2:B8:73
a=group:BUNDLE 1
m=audio 0 UDP/TLS/RTP/SAVPF 0
a=candidate:1276778842 1 udp 2130706431 192.168.10.107 56532 typ host
a=candidate:1276778842 2 udp 2130706431 192.168.10.107 56532 typ host
a=candidate:103462405 1 udp 1694498815 192.168.2.222 2864 typ srflx raddr 0.0.0.0 rport 53804
a=candidate:103462405 2 udp 1694498815 192.168.2.222 2864 typ srflx raddr 0.0.0.0 rport 53804
a=end-of-candidates
m=video 9 UDP/TLS/RTP/SAVPF 96
c=IN IP4 0.0.0.0
a=setup:active
a=mid:1
a=ice-ufrag:ZFQNenOmjxUdYjPs
a=ice-pwd:YnftadoLNioHqISApdSCmiPQDdsjuLxc
a=rtcp-mux
a=rtcp-rsize
a=rtpmap:96 VP8/90000
a=rtcp-fb:96 goog-remb 
a=rtcp-fb:96 transport-cc 
a=rtcp-fb:96 ccm fir
a=rtcp-fb:96 nack 
a=rtcp-fb:96 nack pli
a=ssrc:2728159949 cname:pion
a=ssrc:2728159949 msid:pion video
a=ssrc:2728159949 mslabel:pion
a=ssrc:2728159949 label:video
a=msid:pion video
a=sendrecv
"
}
## plan-b vs UnifiedPlan
1. plan-b 要求所有的A/V流都使用相同的编码，UnifiedPlan不需要。
1. 当只有一路音频视频的时候，两者兼容
1. plan-b --> UnifiedPlan, 反之不行。  
## 小技巧
### rfc 标准状态查询
[查询4092](ttps://www.rfc-editor.org/info/rfc4092)  
直接替换最后的标准标号能得到该标准现在的状态.  
如果被废弃也能知道新的是谁.  
### 特定字段所属标准查询
对于sdp中不明白的key, 可以通过 ‘rfc a=k:'的方式搜索到。由显示格式的那份文档定义
Eg: rfc a=extmap: