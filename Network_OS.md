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
原始的 HTTP 是打开 TCP，发送一个 HTTP，关闭 TCP；
HTTP1.1 开始默认开启 Connection: keep-alive，复用 tcp 连接
HTTP2 将文本转二进制，多个请求共享 tcp 连接
HTTP3 支持 quic(UDP)协议

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

MSS 最大报文段长度：单个报文段（Segment）中 TCP 数据部分所能承载的最大字节数，不包括 TCP/IP 头部。在 TCP 三次握手时，双方会通过 MSS 选项告诉对方自己能接收的最大数据段大小，最终取两者的较小值作为连接的 MSS。  
RTT：从发送方发出一个数据包开始，到收到接收方对该包的 ACK 确认为止所经历的时间。

**三次握手**：SYN -> SYN/ACK -> ACK(可携带数据)

**为什么要三次握手**

1. 客户端没收到 SYN/ACK，导致服务端资源浪费
2. 防止旧的 syn 传到了服务器，保证客户端相应 syn/ack 才算建立连接

**四次挥手**：FIN -> ACK(客户端进入 fin_wait_2 状态) -> FIN/ACK -> ACK(客户端进入 TIME_WAIT 状态，等 2MSL 后关闭连接)

**为什么要四次挥手**  
一次 FIN/ACK 只是把一边的连接断开，而 TCP 是双方都可以发消息接消息，因此需要双方都 FIN  
**谁处于 timewait 状态？**  
主动关闭连接的一方，在发完四次挥手的最后一步时，进入 timewait 状态，等 2MSL  
**为什么要等 2MSL(最长报文段寿命)？**
第一，为了保证 A 发送的最有一个 ACK 报文段能够到达 B。这个 ACK 报文段有可能丢失，因而使处在 LAST-ACK 状态的 B 收不到对已发送的 FIN 和 ACK 报文段的确认。B 会超时重传这个 FIN 和 ACK 报文段，而 A 就能在 2MSL 时间内收到这个重传的 ACK+FIN 报文段。接着 A 重传一次确认。
第二，<源 IP, 源端口, 目标 IP, 目标端口>如果立即重用这个四元组建立新连接，旧连接中的延迟报文可能会被误认为是新连接的数据。等 2MSL 保证旧连接的数据在网络中消失。  
**如何控制拥塞**
cwnd 变量（控制在未收到 ack 之前，往网络中发多少数据）
慢启动：初始值较小，每收到一个 ACK 就+1，每个 RTT 会指数级增加
拥塞避免：达到 sstheshold 慢启动阈值后，每个 RTT 线性增加
如果丢包，看是超时还是 3 次重复 ack
遇到超时重传：ssthreshold 变为 cwd 的一半，cwnd 变为 1
快速重传：收到 3 个重复 ACK，表示某个报文丢失，直接重传，进入快速恢复，将 sshtred 变为 cwnd 的一半，
超时重传计时器：TCP 发送需要确认的报文，就给他启动一个超时重传计时器，到期了还没 ack，就重传。超时事件是根据往返延时动态决定的

## UDP

header(8B) 原端口 目的端口 长度 checksum  
如果接收方 UDP 发现收到的报文中的目的端口号不正确（不存在对应端口号的应用进程 0,），就丢弃该报文，并由 ICMP 发送“端口不可达”差错报文给对方。

| tcp                 | udp            |
| ------------------- | -------------- |
| connection-oriented | connectionless |
| reliable ordered    | not            |
| 16B header          | 8B header      |

## tcp udp 粘包拆包丢包怎么解决

粘包拆包：多个消息可能会在接收端“粘”在一起，或者一个消息被“拆”成几段接收，因为 TCP 是面向字节流，没有包的概念；发送端可能优化，小数据包被合并发送；接受端读取不及时
解决：固定长度，接收端读取固定长度；使用分隔符；消息头+消息体

## QUIC

the foundation of HTTP/3
Runs over UDP
Built-in encryption
Allowing multiple streams within a single connection
Reducing handshake time

## 浏览器输入 url 发生什么

1. dns query
2. 如果是 http，就发起 tcp 连接，向目标服务器发送 http 请求与返回
3. 如果是 https，就发起 tcp 连接，发起 tls 握手，验证服务器证书，交换会话密钥，发送用会话密钥加密的 http 请求与返回

## server 可以维持多少 TCP 连接？

TCP 连接由 4 元组决定{local IP, local port, remote IP, remote port}
理论上一个 IP 可以有 65536 个 port(16bit)，可以有 2\*\*32 个 IP，一共是 2\*\*48 个连接，但是实际上取决于

1. OS 配置：Linux 取决于 ulimit, file-max, and ip_local_port_range， windows 取决于外部设备端口范围和
2. 文件描述符(file descriptor)：一个 TCP 连接需要一个文件描述符，linux 默认 1024 但是可增加
3. 内存：一个 TCP 连接消耗 3KB，8GM 内存的 server 可以维护 1m 个

现实中，一般的 linux 服务器可以维护 10w-1m，windows 一般小于 50w

## API 限流算法

### 固定窗口限流算法

首先维护一个计数器，将单位时间段当做一个窗口，计数器记录这个窗口接收请求的次数。  
当次数少于限流阀值，就允许访问，并且计数器+1  
当次数大于限流阀值，就拒绝访问。  
当前的时间窗口过去之后，计数器清零。

```java
  /**
     * 固定窗口时间算法
     * @return
     */
    boolean fixedWindowsTryAcquire() {
        long currentTime = System.currentTimeMillis();  //获取系统当前时间
        if (currentTime - lastRequestTime > windowUnit) {  //检查是否在时间窗口内
            counter = 0;  // 计数器清0
            lastRequestTime = currentTime;  //开启新的时间窗口
        }
        if (counter < threshold) {  // 小于阀值
            counter++;  //计数器加1
            return true;
        }

        return false;
    }
```

### 滑动窗口限流算法

滑动窗口限流解决固定窗口临界值的问题。它将单位时间周期分为 n 个小周期，分别记录每个小周期内接口的访问次数，并且根据时间滑动删除过期的小周期。

```java
 /**
     * 单位时间划分的小周期（单位时间是1分钟，10s一个小格子窗口，一共6个格子）
     */
    private int SUB_CYCLE = 10;

    /**
     * 每分钟限流请求数
     */
    private int thresholdPerMin = 100;

    /**
     * 计数器, k-为当前窗口的开始时间值秒，value为当前窗口的计数
     */
    private final TreeMap<Long, Integer> counters = new TreeMap<>();

   /**
     * 滑动窗口时间算法实现
     */
    boolean slidingWindowsTryAcquire() {
        long currentWindowTime = LocalDateTime.now().toEpochSecond(ZoneOffset.UTC) / SUB_CYCLE * SUB_CYCLE; //获取当前时间在哪个小周期窗口
        int currentWindowNum = countCurrentWindow(currentWindowTime); //当前窗口总请求数

        //超过阀值限流
        if (currentWindowNum >= thresholdPerMin) {
            return false;
        }

        //计数器+1
        counters.get(currentWindowTime)++;
        return true;
    }

   /**
    * 统计当前窗口的请求数
    */
    private int countCurrentWindow(long currentWindowTime) {
        //计算窗口开始位置
        long startTime = currentWindowTime - SUB_CYCLE* (60s/SUB_CYCLE-1);
        int count = 0;

        //遍历存储的计数器
        Iterator<Map.Entry<Long, Integer>> iterator = counters.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry<Long, Integer> entry = iterator.next();
            // 删除无效过期的子窗口计数器
            if (entry.getKey() < startTime) {
                iterator.remove();
            } else {
                //累加当前窗口的所有计数器之和
                count =count + entry.getValue();
            }
        }
        return count;
    }
```

### 漏桶算法

漏桶算法面对限流，就更加的柔性，不存在直接的粗暴拒绝。

它的原理很简单，可以认为就是注水漏水的过程。往漏桶中以任意速率流入水，以固定的速率流出水。当水超过桶的容量时，会被溢出，也就是被丢弃。因为桶容量是不变的，保证了整体的速率。

```java
/**
     * 每秒处理数（出水率）
     */
    private long rate;

    /**
     *  当前剩余水量
     */
    private long currentWater;

    /**
     * 最后刷新时间
     */
    private long refreshTime;

    /**
     * 桶容量
     */
    private long capacity;

    /**
     * 漏桶算法
     * @return
     */
    boolean leakybucketLimitTryAcquire() {
        long currentTime = System.currentTimeMillis();  //获取系统当前时间
        long outWater = (currentTime - refreshTime) / 1000 * rate; //流出的水量 =(当前时间-上次刷新时间)* 出水率
        long currentWater = Math.max(0, currentWater - outWater); // 当前水量 = 之前的桶内水量-流出的水量
        refreshTime = currentTime; // 刷新时间

        // 当前剩余水量还是小于桶的容量，则请求放行
        if (currentWater < capacity) {
            currentWater++;
            return true;
        }

        // 当前剩余水量大于等于桶的容量，限流
        return false;
    }
```

### 令牌桶(token bucket)

面对突发流量的时候，我们可以使用令牌桶算法限流。

有一个令牌管理员，根据限流大小，定速往令牌桶里放令牌。  
如果令牌数量满了，超过令牌桶容量的限制，那就丢弃。  
系统在接受到一个用户请求时，都会先去令牌桶要一个令牌。如果拿到令牌，那么就处理这个请求的业务逻辑；  
如果拿不到令牌，就直接拒绝这个请求。

```java
   /**
     * 每秒处理数（放入令牌数量）
     */
    private long putTokenRate;

    /**
     * 最后刷新时间
     */
    private long refreshTime;

    /**
     * 令牌桶容量
     */
    private long capacity;

    /**
     * 当前桶内令牌数
     */
    private long currentToken = 0L;

    /**
     * 漏桶算法
     * @return
     */
    boolean tokenBucketTryAcquire() {

        long currentTime = System.currentTimeMillis();  //获取系统当前时间
        long generateToken = (currentTime - refreshTime) / 1000 * putTokenRate; //生成的令牌 =(当前时间-上次刷新时间)* 放入令牌的速率
        currentToken = Math.min(capacity, generateToken + currentToken); // 当前令牌数量 = 之前的桶内令牌数量+放入的令牌数量
        refreshTime = currentTime; // 刷新时间

        //桶里面还有令牌，请求正常处理
        if (currentToken > 0) {
            currentToken--; //令牌数量-1
            return true;
        }

        return false;
    }
```

## select, poll, epoll 的区别

他们都是用来监听多个 socket 读写事件
select: 最大连接数有限制，一般是 1024（可增大不建议），事件通知方式为轮询所有 fd，每次调用需复制整个 fd 数组
poll：最大连接数理论上没有限制，事件通知方式为轮询所有 fd，每次调用需复制整个 fd 数组
epoll：最大连接数理论上没有限制，事件通知方式为回调通知，只需注册一次即可

## epoll 水平触发 LT，边缘触发 ET

水平：只要缓冲区有数据，epollwait 就会持续返回事件，所以会频繁触发，可阻塞可非阻塞  
边缘：只有状态改变了（不可读变为可读）才触发事件，必须非阻塞，必须一次性读完所有数据，不然会丢事件

## socket 通信流程

**tcp**  
服务端：创建 socket，bind()绑 ip 端口，listen()监听等待连接, accept()接收连接, recv()/send(),close  
客户端：创建 socket，connect(),send()/recv(),close()

**udp**
创建，bind(),如果 connect 就 send()/recv(),如果不 connect 就 sendTo/recvFrom，close  
connect 的好处是简化代码，不用每次拷贝地址到内核，能接受到 ICMP 目标不可达错误

## RPC remote procedure call 远程过程调用

像本地调用一样去调用远程服务（方法调用封装，参数序列化，网络传输，远程执行，结果返回与反序列化）
**关键点**：
服务寻址：如何找到远程服务的地址（IP、端口、注册中心）
序列化/反序列化：将参数和结果转换为可传输的格式（如 JSON、Protobuf、Thrift）
网络传输协议：tcp，http，grpc
动态代理：客户端通过代理对象调用，封装了底层通信细节

## RPC 服务发现机制

**服务发现基本流程**  
服务注册：服务提供者启动时将自己的服务信息（ip，端口，服务名）注册到注册中心，注册中心保存这些信息  
服务订阅：服务消费者启动时，向注册中心订阅，注册中心将可用服务实例返回  
服务发现：消费者根据返回的结果，选择一个或多个进行远程调用，通常结合负载均衡策略（轮询，权重，哈希）来选择服务节点  
服务下线与健康检查：服务实例宕机或下线时，注册中心会更新服务列表，消费者通过监听或定期拉取，感知变化

**服务变更通知**
主动拉取：客户端定时从注册中心拉取服务列表，简单但实时性差  
被动订阅：客户端订阅服务变更，注册中心通过回调或者通知更新  
混合模式：客户端启动时拉取一次服务列表，后续通过订阅接受变更

## websocket

握手阶段：客户端发起一个 HTTP 请求，带上 Upgrade: websocket 头部

协议切换：服务器返回 101 Switching Protocols，连接升级为 WebSocket

持久连接：连接建立后，客户端和服务器可以任意时刻互发消息

数据帧传输：消息被封装成帧（frame），支持文本和二进制格式

关闭连接：任一方可发送关闭帧，断开连接

## tls 握手

为了确认服务器可信，协商加密算法，交换密钥
client Hello（客户端发送支持的版本号、随机数） -> server Hello（服务器选择 cipher suit，返回随机数和数字证书） -> 证书验证 -> 密钥交换 -> 会话密钥生成

## 容灾限流

容灾处理

1. 当服务连续失败超过阈值，熔断，拒绝请求，避免雪崩，常用工具：reslience4j，sentinel
2. 降级，服务不可用时返回默认值或静态页面
3. 重试，适用于瞬时故障，设置最大次数与间隔
4. 服务隔离，防止一个模块故障影响其他模块
5. 健康检查，异常时自动下线

## ARP 地址转换协议（ip 转 mac）

先查本地 ARP 缓存，查不到就广播到同一个子网（包括源 ip mac 和目标 ip），子网内每台机器检查是否是自己，是的话就单播返回（自己的 mac 和 ip），主机更新 ARP 表  
不是同一子网，就先 ARP 网关，发给网关，网关会找到目标 MAC 并转发

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

coroutine: 用户态的，轻量级线程。上下文切换开销低，只需保存执行状态，是在单线程内切换. 由程序负责切换而不是 os，

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

## 虚拟内存 / 物理内存 / 页表

虚拟内存：os 通过 MMU 模拟出来的抽象地址空间，程序认为自己有连续且充足的空间
物理内存：真实存在的硬件内存，是 CPU 可直接访问的
页表：将虚拟地址映射到物理地址，每个进程有自己的页表，如果页不在内存中，则触发缺页中断。一般 CPU 先查 TLB（Translation Lookaside buffer），再查页表
TLB：缓存虚拟页号到物理页号的映射

## 图灵完备

一系列数据操作的规则（如指令集，编程语言，细胞自动机）可用来模拟单带自动机，那么他是图灵完备的。

## CAP 理论

分布式系统的核心原则，不可能同时满足三个特性，
Consistency 一致性：所有节点都能拿到最新的数据，牺牲一致性的有 cassandra，dynamodb
availability 可用性：每个请求都能在有限时间里获得响应，牺牲可用性的有 Hbase 和 zookeeper
partition tolerance 分区容忍性：系统在部分节点失效时仍能提供服务

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

## 进程调度算法

FCFS，优先级，RR，短作业优先 SJF，最短剩余时间优先 SRTF
抢占？非抢占？

## 一个线程占多大内存

由栈空间，TCB 线程控制块，线程私有数据 Thread local storage 组成，java 默认 1MB，linux glibc 默认 8MB

## linux 命令

```bash
ps -ef  # 查看所有进程（标准格式）
top #实时资源监控
free -h #查看内存

```

## 大端小端（Big / small endian）

大：高位放低字节，就像人看的一样
地址： 0x1000 0x1001 0x1002 0x1003  
数据： 12 34 56 78

小：高位放高字节

如何判断？

```c
#include <stdio.h>

int main() {
    int a = 1;
    char *p = (char *)&a;
    if (*p == 1)
        printf("小端模式\n");
    else
        printf("大端模式\n");
    return 0;
}
// 看1是在最高还是在最低
```

## 用户态，内核态

两种运行级别，代表程序在不同权限下运行的状态。防止用户直接操作硬件或内核数据，且用户程序崩了不影响系统
什么时候切换到内核态？系统调用如 read(), write()了；遇到异常如除 0，缺页，进入内核态执行异常处理程序；中断，如外设就绪向 cpu 发送中断

## 三层 cache 延迟时间（以 3GHz 主频换算）

L1：3-5 cycle， 1ns，每个核心独享，容量小（32-128KB）
L2：10-20 cycle， 3-7ns，每个核心独享或半共享，256KB-1MB
L3：30-50 cycle，10-20ns，多核心共享，2-64MB
主存 DRAM：200-300 cycle，70-100ns，GB 级别的容量

## 服务器 cpu 占用高的原因 如何排查

应用层：死循环或递归，频繁 GC，SQL 全表扫描
系统层：磁盘/网络 IO 阻塞，锁竞争，僵尸进程
外部因素：ddos，木马或恶意脚本

使用 top 查看高 CPU 进程
使用 jstack 导出线程栈，查看 runnable 线程状态

```bash
jstack -l <PID> > thread_dump.txt
```

```text
"Thread-1" #12 prio=5 tid=0x00007f8b4800e800 nid=0x1a03 waiting on condition
   java.lang.Thread.State: WAITING (parking)
   at sun.misc.Unsafe.park(Native Method)
   at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
   at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued(...)

```

## 服务器内存占用高的原因，如何排查

应用层：内存泄漏，缓存未清理，数据处理过大
系统层：文件占用，僵尸进程
外部因素：木马攻击

free 命令查看内存使用情况
top 找出高占用进程
jmap 分析堆内存

```bash
jmap -heap <pid> #查看堆内存结构
jmap -histo[:live] <pid> #查看对象直方图（按类统计）
# num     #instances         #bytes  class name
# 1:      1000000            200000000   [B
# 2:      500000             80000000    java.lang.String
jmap -dump:live,format=b,file=heapdump.hprof <pid> # 生成堆转储文件（Heap Dump）
```

使用 MAT 查看 hprof 文件，查看：哪些对象占用最多内存，哪些对象有长引用链（GC Root → 大对象），是否存在缓存未清理、静态变量持有等问题

## 硬链接 vs 软链接

硬连接：指向文件的 inode（记录了文件的元数据），增加引用计数，当引用归零，数据块才被释放  
软链接：独立的小文件，内容是目标文件的路径字符串

## 僵尸进程、孤儿进程如何产生

父进程没有回收子进程的状态，导致进程表里保留了子进程记录
父进程结束，子进程还在

## 管道和重定向是怎么实现的

重定向：改变文件描述符的指向，ls > out.txt 就是先打开文件，把标准输出指向这个文件
管道：内存缓冲区，有读写两端
其实两者都是操作文件描述符表，前者是把 fd 指向文件/设备，后者是把 fd 指向内核的 pipe 缓冲区

## 用户态调用 read 读文件，中间的内核处理过程是怎样的

read - glibc syscall 切换到内核态 - sysread - vfsread（虚拟文件系统的统一入口，检查是否可读） - 查询页缓存 - copy 到用户态内存空间 - 回到用户态

## linux 文件系统底层管理机制

vfs 虚拟文件系统，统一抽象，避免不同文件系统的差异
inode 索引节点/block 数据块 元数据与数据分离
页缓存：缓存文件数据页，减少磁盘访问
日志

## os 加载过程

自检
加载 bootloader
加载内核，内核初始化
挂载真实根文件系统
启动第一个用户进程 `/lib/systemd/systemd`
初始化，挂载剩余的文件系统，启动必要系统服务如网络，日志，定时任务
登陆
