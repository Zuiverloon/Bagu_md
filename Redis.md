# Redis

一个 key-value 的非关系型数据库

## how to use?

```shell
docker run -itd --name redis -v /Users/zjy/Desktop/Workspace/RedisContainerData:/data -p 6379:6379 redis
```

Run the image in a container and mount the data directory(in container) with my local directory.

JAVA 连接：

```java
        Jedis jedis = new Jedis("localhost",6379);
        System.out.println("Ping: "+jedis.ping());
```

CLI 连接：进入容器 执行 redis-cli

**redis 五种数据类型：string，hash，list，set，sortedset**

## string

string 类型最大存 512MB

```
redis 127.0.0.1:6379> SET runoob "菜鸟教程"
OK
redis 127.0.0.1:6379> GET runoob
"菜鸟教程"
```

string 的底层存储由 SDS Simple Dynamic String 实现，根据内容自动选择 int，embstr(<=44B，且只读，redisObject 与 SDS 连续分配)，raw 编码(>44B，redisObject 与 SDS 分开存放)，获取长度高效，可动态扩容

```c
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len;      // 已使用长度
    uint8_t alloc;    // 分配的总长度
    unsigned char flags; // 类型标志
    char buf[];       // 实际字符串内容
};

```

## hash（小型用 Ziplist，大型用 HashTale）

ziplist 是紧凑的双向链表，但是是连续的内存块，没有指针指向下一个节点
键值对集合，用于存储对象，每个 hash 可以存 2\*\*32-1 个键值对

```
redis 127.0.0.1:6379> HMSET runoob field1 "Hello" field2 "World"
"OK"
redis 127.0.0.1:6379> HGET runoob field1
"Hello"
redis 127.0.0.1:6379> HGET runoob field2
"World"
```

## list（双向链表，小型用 Ziplist，大型用 quicklist（链表+ziplist））

字符串列表，可以头插也可以尾插，每个列表可存储 2\*\*32-1 个元素

```
redis 127.0.0.1:6379> lpush runoob redis
(integer) 1
redis 127.0.0.1:6379> lpush runoob mongodb
(integer) 2
redis 127.0.0.1:6379> lpush runoob rabbitmq
(integer) 3
redis 127.0.0.1:6379> lrange runoob 0 10
1) "rabbitmq"
2) "mongodb"
3) "redis"
```

## set（IntSet（一种升序排列的数组）， HashTable）

集合，通过哈希表实现，插入删除都是 O(1) 集合中最大的成员数为 2\*\*32 - 1

```
redis 127.0.0.1:6379> sadd runoob redis
(integer) 1
redis 127.0.0.1:6379> sadd runoob mongodb
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabbitmq
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabbitmq
(integer) 0
redis 127.0.0.1:6379> smembers runoob

1) "redis"
2) "rabbitmq"
3) "mongodb"
```

## sortedset（跳表 skiplist（有多级索引的链表） + hashmap）

给每一个元素一个分值，分值可以重复但是元素不能重复

```
redis 127.0.0.1:6379> zadd runoob 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 mongodb
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabbitmq
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabbitmq
(integer) 0
redis 127.0.0.1:6379> ZRANGEBYSCORE runoob 0 1000
1) "mongodb"
2) "rabbitmq"
3) "redis"
```

hashmap 用于快速查找某个元素的分数
跳表按分数排序元素，支持范围查询

## 发布订阅

生产者可以给 channel 发送消息，消费者可以从 channel 拿到消息

```
PUBLISH channel1 "publish message"
SUBSCRIBE channel1
```

## redis 事务

redis 一次可以执行多个事务  
在收到 exec 命令前把任务放入队列  
收到 exec 命令后开始执行事务，且新提交的任务不会被插入当前执行的任务中  
单个 redis 命令的执行是原子的，但是事务不是原子的，有一个命令执行失败，不会影响别的命令的运行，也不会触发之前的命令的回滚

## redis 单线程为什么快？

单线程指的是数据处理为单线程，redis 服务器与客户端通信是 tcp，如果一个请求的数据还没有全部接收到，redis 不会去处理这一部分的数据，这样 redis 就不会因为 io 而被阻塞。单单处理数据的时间是很快的，不会造成长时间的排队或阻塞。单线程还可以避免并发问题。

## **Redis 过期**

设 key 的时候有设置过期时间，可以是定期删除过期 key，也可以是获取 key 的时候检查是否过期

## 过期策略

1. 惰性删除：客户端访问某个 key，检查该 key 是否过期。不主动消耗 CPU，但是过期的 key 可能长期占用内存
2. 定期删除：每隔一段时间抽取设置了过期时间的 key 检查。主动清理避免内存滞留，但无法保证所有 key 都及时删除
3. 内存淘汰：当 redis 使用的内存到达限制时，淘汰部分 key。淘汰策略：volatile-lru（设置了过期时间的 key 中淘汰最近最少使用的），allkeys-lru（所有 key 中最近最少使用的），volatile-ttl（淘汰剩余存活时间最短的），noeviction（不淘汰，但是拒绝写入），volatile-lfu（设置了过期时间中使用频率最少的），allkeys-lfu，allkeys-random，volatile-random

## 缓存穿透

数据既不在 db 也不在 redis，导致每次都到 db  
解决办法：1. 空对象也缓存， 2. 布隆过滤器（多个 hash+位数组，进行多次 hash，如果有一个 0，肯定不存在，但是会误判存在），拦截肯定不存在的 key

## **缓存击穿**

热点数据过期，大量请求打到 db。解决办法：1.互斥锁，只有一个请求到 db，其他等待或重试， 2. 设置逻辑过期：过期了就返回旧值，直到更新缓存 3. 热点数据不过期

## **缓存雪崩**

一大批数据同时过期，解决办法：过期时间均匀分布，热点不过期

## **持久机制**（RDB 快照 / AOF 日志）

**rdb**
生成快照存在.rbd 文件中，可以通过 save 命令或者配置自动触发，使用 fork 子进程+写时复制，不阻塞主线程  
优点：文件体积小，加载速度快，对性能影响小  
缺点：数据不是实时的，可能丢失一次 snapshot 后的数据，fork 在数据量大时会造成内存压力

**AOF**
AOF 持久化，把执行过的命令都写入文件，先把命令写在 aof buf 中，用子进程定时写 aof，并且重写 aof 优化冗余的指令。但会遇到新来的命令修改数据的问题，因此需要一个 aofrewritebuf，每次重写完再把 aofrewritebuf 中的内容写入，默认每秒写入
优点：文件内容是 redis 指令，便于审计  
缺点：恢复时间慢，需逐行执行

## 分布式锁

set 命令支持原子性，可用来实现分布式锁

```bash
SET lock_key unique_value NX PX 30000
```

解锁时要校验身份，持有锁的人才可以解锁

```lua
-- Lua 脚本：先判断再删除，保证原子性
if redis.call("GET", KEYS[1]) == ARGV[1] then
  return redis.call("DEL", KEYS[1])
else
  return 0
end

```

redis 内置了 lua 解释器。通过 EVAL 命令将脚本发送到 redis 服务器。lua 整个脚本是保证原子的。redis 会缓存 lua 脚本，发送相同脚本不用重新编译。
但是不具备回滚机制，需要自己设计一致性逻辑

**为啥不用 SETNX+EX？**  
SETNX 是指 key 不存在时设置 key，也可以实现互斥，但是他和 EX 命令是两个命令，可能被打断

## redis 主从同步

单主从/哨兵/集群都涉及主从同步，但是单主从不能自动切换，哨兵不支持数据分片
**全量**  
首次连接/无法增量  
复制 rdb 文件

**增量**
从节点偏移量仍在复制积压缓冲区内
主节点根据 offset 差值，将缺失的命令发给从节点

## redis 哨兵

监控节点状态，在主节点故障时自动完成故障转移
监控：定时给主从节点发送 ping
通知：节点状态变化时，通过发布订阅机制通知其他哨兵、从节点和客户端
故障转移：主节点挂了就选举新的，让其他从节点复制他
配置提供者：客户端从哨兵获取当前主节点

**故障转移流程**

1. 主观下线：某个哨兵认为主节点挂了
2. 客观下线：多个哨兵达成一致，确认主节点挂了
3. 选举 leader 哨兵：多个哨兵选出一个领导者来执行故障转移
4. 选择新节点：选择复制偏移量最大的节点作为新的主节点
5. 重新配置：其他从节点复制主节点，客户端更新连接

## redis 集群模式

分布式解决方案，通过数据分片、主从复制、自动故障转移 实现了扩展和容错  
由主节点和从节点组成  
key 空间划分为 16384 个槽，每个主节点负责一部分  
使用 gossip 协议进行节点状态交换  
客户端发送命令，redis 根据哈希对应到节点，如果请求发送到错误的节点，就返回重定向指令（Moved/ASK）  
每个主节点挂一个从节点，主节点挂了，从节点被选为新的主节点，故障检测由集群内部完成，无需哨兵
