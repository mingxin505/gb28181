# NATN的4种类型
1. 全锥型，即：只要在NAT上开了端口。发这个端口的都转发到内网。 即：1：N
2. 受限锥型，即：曾通过端口向IP发过数据，发向这个端口的都转发到内网。 即：1：N
3. 端口受限锥型， 即： 曾通过端口向其IP+Port发过数据，发向这个端口的都转发到内网。 即：1：N
4. 对称，当远方IP或者端口变化，都重开新端口。即：1：1

# STUN(Session Traversal Utilities for NAT)
1. 简单说就是获取IP:port对儿的过程。
3. 端口保活，通过 binding indication
4. 短效验证机制，协议不传输验证用的账号、密码，由其它协议提前传。eg, sdp的candidate
5. 长效验证机制类似传统账号、密码验证；密码通过和随机数一起HASH后传给SVR，由SVR验证,
1. 容易被离线字典攻击
1. FingerPrint用于区别是不是STUN数据包
1. 它是个协议，只管定义语法，语义，时序。怎么用它不管。
turn/ice是它的两个知名的用法

# TURN(Traversal Using Relay NAT)
1. turn 用于当两个位于对称NAT后的peer打不通的情况。
1. client可以有多种协议，peer只能UDP
2. 使用stun的长效验证机制
3. Permission是指允许使用allocation
4. 5元组 = 协议 + seflIP + selfPort + remoteIP + remotePort
有没有发现，一直在说打洞打不通的情况。那打洞的过程是怎样的？它由ICE规定

# ICE(Interactive Connectivity Establishment)
1. 规范了打洞过程，它是个框架。如果用UML做类比，STUN/TURN是静态图，ICE是动态图。
