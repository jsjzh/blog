## TCP 报文

![](https://img2018.cnblogs.com/blog/1845293/201912/1845293-20191212131430416-1543284115.png)

**序号（sequence number）**：seq 序号，占 32 位，发起方发送数据时对此进行标记

**确认号（acknowledgement number）**：ack 序号，占 32 位，只有 ACK 标志位为 1 时，ack 序号才有效，ack=发送方的 seq+1

**标志位（flags）**：共 6 个

- ACK: 标识确认号（ack）有效
- SYN: 发起一个新连接的标志
- FIN: 释放一个连接的标志
- URG: 标识紧急指针有效
- PSH: 接收方应该尽快将这个报文交给应用层
- RST: 重置连接

## 三次握手

![](https://img2018.cnblogs.com/blog/1845293/201912/1845293-20191212131738006-306904945.png)

![](https://user-gold-cdn.xitu.io/2019/12/12/16ef885014512a55?w=1000&h=592&f=gif&s=346484)

```
客户端 ---> 服务端: SYN=1, seq=x
服务端 ---> 客户端: SYN=1, ACK=1, seq=y, ack=x+1
客户端 ---> 服务端: ACK=1, seq=x+1, ack=y+1
```

## 四次挥手

![](https://img2018.cnblogs.com/blog/1845293/201912/1845293-20191213234439862-587275161.png)

![](https://media4.giphy.com/media/kcxT7i564dhk6YZHAC/giphy.gif)

```
客户端 ---> 服务端: FIN=1, seq=u
服务端 ---> 客户端: ACK=1, seq=v, ack=u+1
服务端 ---> 客户端: FIN=1, ACK=1, seq=w, ack=u+1
// 服务端如果在 1MSL 内收不到客户端的回信，会再发一个 FIN=1, ACK=1 的信息，直到接收到客户端的信息
客户端 ---> 服务端: ACK=1, seq=u+1, ack=w+1
// 客户端还会等待 2MSL，如果时间内又收到了服务端的信息，会再发送一个 ACK=1 的信息，然后再等 2MSL，直到接收不到服务端的信息
```

MSL(Maximum Segment Lifetime): 一段 TCP 报文在传输过程中的最大生命周期。

和三次握手的区别在中间两步，在四次握手中，服务端 ---> 客户端第一次没有返回 FIN，这个时候还有数据在处理，等处理完成后，再发送了一个 FIN，并且 seq 也不同了

为什么客户端还会等待 2MSL？

因为要确认服务端是否收到客户端发出的 ACK=1 报文，如果 2MSL 内没有再收到服务端的消息，客户端就会进入 CLOSED 状态
