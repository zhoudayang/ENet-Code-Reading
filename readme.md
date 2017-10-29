# ENet Code Reading

ENet官网地址：<http://enet.bespin.org/index.html>

文件夹`EnetClinet`和`EnetServer`是在vs上配置的ENet的测试程序

文件夹`enet-1.3.13 source code`是ENet的开源代码

文件夹`vs-code-read`是在visual studio下进行的源码阅读(= =，便于快速了解各个宏定义和结构的定义和用法)

# ENet特性

ENet是一个具有TCP和UDP各自优点的基于UDP封装的一个网络库。

UDP缺少排序，连接管理，带宽管理，包大小限制等。TCP不能同时打开多个套接字故缺少多流的通信，并且由于其缓冲特性，其包管理机制过于复杂。

ENet则致力于将TCP和UDP的优点结合实现一个统一的网络协议库。

## 连接管理 (Connection Management)

ENet提供了一个简单的与外部主机通信的接口。连接的生命周期通过频繁的ping外部主机动态监管，同时通过主机与外部机器的包的往返时间和丢包情况来监管网络状况。

## 排序 (Sequencing)

ENet提供了多个的合理排序的网络包流而不是一个单一的比特流从而简化了不同类型数据的传输。

ENet通过为每个发送的网络包编号来实现对包的排序。这些序号会随着包的发送而增长。ENet保证序列号低的包优先发送，从而确保了所有的网络包都按次序发送。

对于不可靠的网络包，如果具有高序号的网络包已经到达，ENet则会简单的丢弃那些低序号的网络包。这样就保证了网络包到达后就可以立即被接收，从而减少了网络延时。对于可靠的网络包，如果一个高序号的包已经到达，但是之前的包确没有到达，ENet会推迟高序号包的接受直到其之前相应序号的网络包均已到达。

## 通道 (Channels)

既然ENet会推迟可靠网络包的提交来确保网络包的序号，但是无论到达的包是可靠的还是不可靠的，如果之前的网络包是可靠的包，他们都要被推迟提交，这样做可能会造成一些不需要严格保证次序的包同样被推迟提交，从而造成额外的延时。

为了解决上述问题并减少对包的次序的限制，ENet为一个连接提供了多个交流的通道(channel)。每个通道下的网络包可以独立排序，所以一个通道下的包的传送状态不会影响其他通道下的包的传送。

## 可靠性 (Reliability)

ENet为传送的包提供了可靠性选择，并确保外部主机会确认收到所有的可靠的网络包。如果外部主机在特定时间内没有确认收到网络包，ENet会尝试在合理的次数内重传这个网络包。
重传超时的时间会根据失败次数的增加变得更加宽松便于应对网络临时的混乱和拥塞。

## 分裂和重组 (Fragmentation and Reassembly)

ENet在发送包的时候不会考虑包的大小。大的网络包会被分成若干个大小合适的网络包，并在外部机器上重组为发送前的网络包便于远程机器接收。整个过程对于开发者来说都是透明的。

## 聚合 (Aggregation)

ENet包括了大部分协议指令，包括acknowledgement，packet transfer，确保连接的可用性，减少丢包的机会及其可能造成的延时等。

## 适应性 (Adaptability)

ENet为可靠包提供了一个动态适应的数据窗口以确保连接不会大量的网络包淹没掉。它也提供了一个静态的带宽分配机制以确保机器在发送和接受包时不会超过这个机器承载的范围。更近一步，ENet提供了一个动态的阀门来响应网络连接时带来的偏差，通过限制发送发送包的数量来应对各种类型的网络拥塞问题。




