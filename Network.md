# Network

## HTTP

无状态 每次发送 HTTP 请求都认为是从不同客户端发出  
如果要保持会话，用 cookie 和 session（作为额外的信息写在 header 中）
原始的 HTTP 是打开 TCP，发送一个 HTTP，关闭 TCP；HTTP2 开始可复用 tcp(一个 tcp 发多个 http)

### request

方法(get/post/put/delete) 路径(Path) 版本号  
headers  
body

### response

版本号 状态码 状态消息  
headers  
body

### 常见状态码：

200 - 请求成功 OK  
301 - 资源（网页等）被永久转移到其它 URL Moved Permanently  
404 - 请求的资源（网页等）不存在 Not Found  
500 - 内部服务器错误 Internal Error

### get 和 post 区别？

get 的参数写在 URL 中，不安全，对所有人可见；且 URL 的长度有限制，参数可被保存历史，可缓存  
POST 参数在消息主体中被发送，无长度限制，参数不会被保存历史或日志中，url 中看不到，用于上传文件

## TCP

header： 16B
Awindow：flow control，防止接收端接收太慢  
CWindow：congestion control，防止网络拥堵

**三次握手**：SYN -> SYN/ACK -> ACK(可携带数据)

**为什么要三次握手** 防止已经失效的请求连接报文突然传到了服务器，导致错误。如果只有两次握手，一个很老的请求连接报文可能由于网络阻塞而刚刚发到服务器，服务器便会同意连接，建立连接，然后等客户端发消息，但是客户端根本没有消息要发。在三次握手模型中，只有第三次握手发送到了服务端，才是真正的建立了连接。

**四次挥手**：FIN -> ACK -> FIN/ACK -> ACK

**为什么要四次挥手**  
一次 FIN/ACK 只是把一边的连接断开，而 TCP 是双方都可以发消息接消息，因此需要双方都 FIN  
为什么要等 2MSL(最长报文段寿命)？
第一，为了保证 A 发送的最有一个 ACK 报文段能够到达 B。这个 ACK 报文段有可能丢失，因而使处在 LAST-ACK 状态的 B 收不到对已发送的 FIN 和 ACK 报文段的确认。B 会超时重传这个 FIN 和 ACK 报文段，而 A 就能在 2MSL 时间内收到这个重传的 ACK+FIN 报文段。接着 A 重传一次确认。
第二，就是防止上面提到的已失效的连接请求报文段出现在本连接中，A 在发送完最有一个 ACK 报文段后，再经过 2MSL，就可以使本连接持续的时间内所产生的所有报文段都从网络中消失。

## UDP

header 8B 原端口 目的端口 长度 checksum  
如果接收方 UDP 发现收到的报文中的目的端口号不正确（不存在对应端口号的应用进程 0,），就丢弃该报文，并由 ICMP 发送“端口不可达”差错报文给对方。