# Wireshark网络分析的艺术

# 1. 读者问

## 像福尔摩斯一样思考

![](QQ20160929-1.png)

点击每一个包都会显示这些属性

1. Identification 如果第一次发过来0,以后一般都是0
2. TTL默认开始是64,而中间经过了多少代理就会-1



## 来点有深度的

延迟确认发生太多会影响性能

可通过`tcp.analysis.ack_rtt > 0.2 and tcp.len ==0`来显示出有可能延迟确认带来的包

![小米](QQ20160929-0.png)


这里我自己连接小米路由器的TCP,有非常多的小米延迟连接,所以我时候也会卡之类d的

## 三次握手的小知识

客户端->SYN seq=X ->服务器

客户端<-SYN seq=Y,Ack=X+1<-服务器

客户端->Seq=X+1 ACK=Y+1->服务器

![tcp](QQ20160930-0.png)

这个是我打开一个网页.然后捕抓src和dst为访问网页的IP

然后前3个就是3次握手

可以看看 Seq和ACK值的变化和我刚的描述变化是一样的

当然这个数字太长,可以默认初始值都为0

`Perferences->Protocols->TCP->勾选Relative Sequence Numbers`

这样设置后,成功的握手都是一样的,但是失败的握手就不一样了

失败握手一般分为两种情况

1. 被拒绝
2. 丢包

一般获取失败握手的表达式可以设置为

`(tcp.flags.reset==1) && (tcp.seq ==1)`

或者

过滤重传的握手请求: `(tcp.flags.syn == 1) && (tcp.analysis.retransmission)`

找到了之后,右键追踪TCP流

这是我的一个被拒绝得TCP流

![被拒绝的tcp](QQ20161008-0.png)

但是这样没办法判断丢包是

1. 没传到服务器就丢了
2. 还是服务器接受到了,但是回传的时候丢了

所以最好在客户端和服务器同时抓包

说说DDoS

原理就是大量主机发送SYN请求给服务器,假装建立TCP,这些SYN请求可能包含假的源地址,所以服务器永远收不到Ack,就会留下half-open的连接,每个TCP占用一定的系统资源,建立多了服务器资源就会被耗光

## 被误解的TCP

TCP并非每一个数据包都有对应的Ack

因为延迟确认可以多个包对应一个Ack

TCP VS UDP

误解是

TCP效率低因为要往返确认Ack

而UDP无需确认,就效率高

但是这是区分场景的

在传送大包时,只要窗口足够大,整个通道充满着TCP,只要窗口足够大,TCP也可以不受往返的约束而源源不断的传输数据

而在传输小包时,本来一个往返就能完成的,却要耗费3次握手和4次挥手,效率的确很低

## 最经典的网络问题

Wireshark性能三把虎

1. 查看平均流量速度: Statistics->Summary
2. 查看建议: Analyze->Expert Infos
3. 查看数据传输: 选中某个包,Statistics->Tcp StreamGraph->TCP Sequence Graph(Stevents),正常的传输是一条直线,中间无中断过程

纳格(Nagle)算法: 解决小包问题

```javascript
if 有新数据要发
  if 数据量超过MMS(即一个TCP包所能携带的最大数据量)
    立即发送
  else
    if 之前发出去的数据尚未确认
      把数据缓存亲来,凑够MSS或等待确认到达再发送
    else
      立即发送
    else if
  else if
else if  
```

## 为什么丢单子

Linux用户访问挂载文件夹

用户同时在20个群组

现在分别有20个群组权限的文件夹

用户只能打开前16个,而后4个不能打开

不通过挂载,而在本地文件夹设置权限,20个都可以打开

在RFC 5532 发现了gids<16>的代码,即访问挂载点要PRC层传递gids

而本地访问文件夹是不需要的

解决方式有

1. 把客户端的/etc/passwd和/etc/group复制到服务器上,但是问题是客户端修改了群组,而忘记同步到服务器时,就会有问题

### 虚惊一场

4次挥手

```
服务器 -> FIN Seq=X Ack=Y(我想断连接) -> 客户端
服务器 <- Seq=Y,Ack=X+1(知道了,断开吧) <- 客户端
服务器 <- FIN Seq=Y,Ack=X+1 (我也想断连接) <- 客户端
服务器 -> Seq=X+1,Ack=Y+1(知道了,断开吧) -> 客户端
```

延迟确认可以把4次挥手减少到3次

## Wireshark的提示

> 1.Packet size limited during capture

一般是因为抓包引起的,tcpdump默认只抓96个字节,可以通过-s设置

> 2.TCP Previous segment not captured

后一个包的Seq号为前一个包的Seq+Len(除了三次握手和四次挥手)

如果后一个包的Seq大于前一个包的Seq+Len,证明确实了一段数据

如果整个网络包都找不到(排除错乱),就会提示TCP Previous segment not captured

丢包分两种情况

1. 真的丢了: 无对方回复的Ack
2. 抓包工具没抓到: 有对方回复的Ack

> 3.TCP ACKed unseen segment

可以忽略

> 4.TCP Out-ofOrder

TCP包乱序了

2 1 3 4 5 这样乱没事

但是

2 3 4 5 1 这样的话 会触发Dup Ack 会导致1号包重传

> 5.TCP Dup ACK

因为乱序,

当接收方收到一些Seq比期望值大的包它每接受一个这种的包就会Ack一次期望的值,以此提醒发送方,所以就产生了重复的Ack

> 6.TCP Fast Retransmission

当发送了3个/以上的TCP Dup ACK就意识到包可能丢了,于是快速重传

> 7.TCP retransmission

如果一个包真的丢了 又没有后续包可以在接触方触发Dup Ack,就不会快速重传,这种情况发送方只好等到超时再重传,就会触发Tcp Retransmission

> 8.TCP zerowindow

TCP包中包邮 `win=`表示接收窗口的大小,即表示这个包的发送方当前还有多少缓存区可以接受数据

当win=0 就会打上TCP zerowindow的标记,表示缓存区已满,不接受数据

> 9.TCP window Full

表示这个包的发送方已经把对方所声明的接收窗口耗尽了

TCP window Full: 这个包的发送方暂时没办法再发送数据了

TCP zerowindow: 这个包的发送方暂时没有办法再接受数据了

两者都以为着传输暂停,需要引起重视

> 10.TCP segment of a reassembled PDU

当你接受到这个提醒

`Edit-Preferences-Protocols-TCP菜单`中启用了Allow sub dissector to reassemble TCP streamss

表示可以把属于同一应用层的PDU的TCP包虚拟的集中起来

> 11.Continuation to

这个表示你关闭了`Edit-Preferences-Protocols-TCP菜单`中的Allow sub dissector to reassemble TCP streamss

> 12.Time-to-live-exceeded(Fragment reassembly time exceeded)

ICMP报错种类很多,但不难理解

# 工作中的Wireshark

## 书上错了吗

在服务器上因为网络延迟,包的顺序可能不会和客户端一抹一样

## 计算在途字节数

网络承载量: 已经发出去,但尚未被确认的字节数

如果超载了,就会丢包重传

在途字节数 = Seq + Len -Ack

Seq和Len书来自上一个数据发送方的包,Ack则是上一次数据接收方的包

## 估算网络拥塞点

找到拥塞点,拥塞的特征是大量重传

先从Wireshark中找到一连串重传包的第一个,再根据该重传包的Seq
值找到原始包,最后计算该原始包发送时刻的在途字节数

1. Analyze->Expert Info->Notes,得到重传列表,找到第一个retransmission(或者过滤`tcp.analysis.retransmission`)
2. 记录其Seq,笔者这次找到的是`4294960297`,过滤`tcp.seq==4294960297`,然后清楚过滤,找到该条记录的上一条来自服务器的包
3. 然后把seq+len-上次服务器的包的ack

## 顺便说说LSO

如果在上面估算网络拥堵点时,发现只见重传包,不见原始包,有可能

1. 这个包实在接收方抓的,看不到已经在路上丢失的原始包很正常
2. 开始抓包的时候,原始包已经传完了,看不到它也很正常
3. Wireshark出BUG,把一个正常的包标记为TCP Fast Retransmission

正常的网络工作方式

1. 传统: 应用层把产生的数据交给TCP,TCP层再根据MSS大小进行分段(CPU负责),然后交给网卡
2. 启用LSO: TCP层就可以把大于MSS的数据直接给网卡,让网卡负责分段工作

## 熟读RFC

懵逼

## 一个你本能解决的问题

TCP在大的数据传输时,可以重组分片

而UDP则无法重组,需要重新发,但理论上也可以重组分片,但是需要研发给力

## 几个关于分片的问题







