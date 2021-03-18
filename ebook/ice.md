# ICE
## 控制端与被控端
主被如何确定?  
若两端有个lite类型的，则它必为主动端;若两端都不是，谁发offer谁是主动端。详见[RFC8445 S6.1.1](http://)  
为什么这么规定呢？  
因为lite端只有HOST地址，如果它是被动，没人能连的上它。

## 连接保持
通过Binding Request 实现连接保持

## 提名
普通提名是candidate 交互完了，再提名   
激进提名是candidate 中包含提名（use_candidate)
## libnice 

