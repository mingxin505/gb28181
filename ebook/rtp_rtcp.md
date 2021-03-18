# rtp_rtcp
这是一对好朋友。互相帮助完成任务。用快递业的比喻就是，rtp 是运输车，只管装东西然后运往目的地，至于里面是黄金白银还是垃圾它不管；rtcp就是货运站工作人员，它负责整理、确认。该重拉的重新去拉，该丢弃的丢弃。
这里就有分歧了。由发贷人控制重传的，术语叫：transport-cc;由收货人控制重传的，术语叫： remb
确认这步术语叫ACK，不确认就叫NACK.由收货人操作。

# nack 
## RTPFB 默认，以包为单位重传
## PSFB(payload selector feed back) 指定净荷重传
## PLI(Picture Loss Indication)
## SLI(slice Loss Indication)
## RPSI(Reference Picture Selection Indication)
# ccm (codec control using RTCP feedback message)
## fir(Full Intra Request )

# transport-wide cc
# remb
# gcc

# abs_time
### 原理， 
根据SR包的NTP时间和RTP包中的abs_send_time_24，共同算出真实时间。  
SR的NTP是完整时间，abs_send_time_24是相对于它的偏移量。
### 缺点，
由于RTP包的起始序列号随机，因此无法知道收到的SR和RTP包的对应关系。  
一种方式是计数，比如，SR说发了10个，那就数10个RTP包，以此对齐。期间注意RTP的sequcenceNumber连续性。  
此种方法有个不足，当连续的起始RTP包丢失，就对比不齐了。