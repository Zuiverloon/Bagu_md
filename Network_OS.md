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

**服务发现的实现方式**
主动拉取：客户端定时从注册中心拉取服务列表，简单但实时性差  
被动订阅：客户端订阅服务比昂更，注册中心通过回调或者通知更新  
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

## 虚拟内存 / 物理内存 / 页表

虚拟内存：os 通过 MMU 模拟出来的抽象地址空间，程序认为自己有连续且充足的空间
物理内存：真实存在的硬件内存，是 CPU 可直接访问的
页表：将虚拟地址映射到物理地址，每个进程有自己的页表，如果页不在内存中，则触发缺页中断。一般 CPU 先查 TLB（Translation Lookaside buffer），再查页表

## 图灵完备

一系列数据操作的规则（如指令集，编程语言，细胞自动机）可用来模拟单带自动机，那么他是图灵完备的。

## 浏览器输入 url 发生什么

1. dns query
2. 如果是 http，就发起 tcp 连接，向目标服务器发送 http 请求与返回
3. 如果是 https，就发起 tcp 连接，发起 tls 握手，验证服务器证书，交换会话密钥，发送用会话密钥加密的 http 请求与返回

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
