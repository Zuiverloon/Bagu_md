# Network

## 5 layer model

Application(HTTP,DNS,SMTP):Interfaces directly with user applications
(7 layer)Presentation(SSL\TLS): manage encryption,compression, encoding
(7 layer)Session: manage session
Transport(TCP,UDP):ensure reliable data transfer between host, handle segmentation and error control
Network(IP,ICMP,ARP):routing and addressing
Data link(Ethernet, WIFI802.11): handle node to node data transfer, manage MAC addressing and error handling
Physical(cable)

## DNS domain name system

find ip address by domain name

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

header(8B) 原端口 目的端口 长度 checksum  
如果接收方 UDP 发现收到的报文中的目的端口号不正确（不存在对应端口号的应用进程 0,），就丢弃该报文，并由 ICMP 发送“端口不可达”差错报文给对方。

| tcp                 | udp            |
| ------------------- | -------------- |
| connection-oriented | connectionless |
| reliable ordered    | not            |
| 16B header          | 8B header      |

## QUIC

the foundation of HTTP/3
Runs over UDP
Built-in encryption
Allowing multiple streams within a single connection
Reducing handshake time

## server 可以维持多少 TCP 连接？

TCP 连接由 4 元组决定{local IP, local port, remote IP, remote port}
理论上一个 IP 可以有 65536 个 port(16bit)，可以有 2\*\*32 个 IP，一共是 2\*\*48 个连接，但是实际上取决于

1. OS 配置：Linux 取决于 ulimit, file-max, and ip_local_port_range， windows 取决于外部设备端口范围和
2. 文件描述符(file descriptor)：一个 TCP 连接需要一个文件描述符，linux 默认 1024 但是可增加
3. 内存：一个 TCP 连接消耗 3KB，8GM 内存的 server 可以维护 1m 个

现实中，一般的 linux 服务器可以维护 10w-1m，windows 一般小于 50w

# OS

## 进程和线程的区别

进程：资源调度的最小单位  
线程：cpu 执行的最小单位  
cpu 只能调度线程  
当创建进程时，系统会创建一个主线程  
同一进程的下的线程可以共享进程资源，除了栈(线程有自己的栈和 cpu 上下文，PC，但是共享代码)
一个进程崩了，不影响其他进程  
一个线程崩了，这个线程所在的进程就崩了

| process                                                    | thread                                                                                 |
| ---------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| any program in execution(at least one thread in a process) | a segment of a process,cpu only schedule thread                                        |
| processes are isolated with each other                     | threads in one process share code,heap but they have independent stacks,PC,cpu context |
| if one crash, others ok                                    | one crash, other threads in the same process crash                                     |

## thread vs coroutine

coroutine: Lightweight function that can be paused/resumed, when doing context switching, only function states need to save. memory usage is low, since coroutines can share stack. cooperative scheduling(like GMP in GO)

## REST(Representational State Transfer)

architecture style when we are implementing backend interfaces or apis.  
use noun to locate the resource,and http method(get/post) to indicate creation,retrieve,update,delete. Data and functionality are regarded as resource.  
JSON is usually used to deliver data to clients.

1. new a friend: POST localhost:8000/friend
2. get a friend: GET localhost:8000/friend
3. modify: PUT localhost:8000/friend
4. delete: DELETE localhost:8000/friend

**Principles**:

1. uniform interface
2. client-server mode (decoupling)
3. stateless
4. cache
5. hierarcy

## 页面置换算法

LRU：最近最久未使用。实现为 index 索引+双向链表  
最优置换(Optimal)：置换最迟访问的页面。不可能实现，因为系统无法预测未来  
先进先出(FIFO)：可能置换掉有用的页

## 页表

store the mapping between virtual address and physical address.

## 图灵完备（MSHF 傻逼公司）

一系列数据操作的规则（如指令集，编程语言，细胞自动机）可用来模拟单带自动机，那么他是图灵完备的。

## 浏览器输入 url 发生什么

1. dns query
2. 如果是 http，就发起 tcp 连接，向目标服务器发送 http 请求与返回
3. 如果是 https，就发起 tcp 连接，发起 tls 握手，验证服务器证书，交换会话密钥，发送用会话密钥加密的 http 请求与返回

## 用 char array 实现 malloc function

```c
void* my_malloc(size_t size) {
    if (heap_index + size > HEAP_SIZE) {
        return NULL; // Out of memory
    }

    void* ptr = &heap[heap_index];
    heap_index += size;
    return ptr;
}

int main() {
    int* a = (int*)my_malloc(sizeof(int));
    if (a) {
        *a = 123;
        printf("C: Allocated int = %d\n", *a);
    }

    char* msg = (char*)my_malloc(6);
    if (msg) {
        snprintf(msg, 6, "Hi!");
        printf("C: Allocated string = %s\n", msg);
    }

    return 0;
}
```

```c++
#include <iostream>
#include <cstring>

constexpr size_t HEAP_SIZE = 1024;

class SimpleAllocator {
private:
    char heap[HEAP_SIZE];
    size_t heap_index = 0;

public:
    void* allocate(size_t size) {
        if (heap_index + size > HEAP_SIZE) {
            return nullptr;
        }
        void* ptr = &heap[heap_index];
        heap_index += size;
        return ptr;
    }
};

int main() {
    SimpleAllocator allocator;

    int* a = static_cast<int*>(allocator.allocate(sizeof(int)));
    if (a) {
        *a = 456;
        std::cout << "C++: Allocated int = " << *a << std::endl;
    }

    char* msg = static_cast<char*>(allocator.allocate(6));
    if (msg) {
        std::strcpy(msg, "Hey!");
        std::cout << "C++: Allocated string = " << msg << std::endl;
    }

    return 0;
}
```

## 进程间通信

管道：单向，匿名(仅父子进程) & 有名(任意进程)  
消息队列：存放在内存中的消息链表  
共享内存：进程可访问同一块物理空间  
信号量+共享内存：可实现互斥访问。P 占用，V 释放  
信号：唯一的异步通信机制，通过发送指定信号通知进程执行信号处理程序  
socket：不同主机上的进程通信
