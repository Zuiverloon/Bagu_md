# 中间件

# redis

一个 key-value 的非关系型数据库

**redis 五种数据类型：string，hash，list，set，sortedset**

## string

string 类型最大存 512MB

```
redis 127.0.0.1:6379> SET runoob "菜鸟教程"
OK
redis 127.0.0.1:6379> GET runoob
"菜鸟教程"
```

## hash

键值对集合，用于存储对象，每个 hash 可以存 2\*\*32-1 个键值对

```
redis 127.0.0.1:6379> HMSET runoob field1 "Hello" field2 "World"
"OK"
redis 127.0.0.1:6379> HGET runoob field1
"Hello"
redis 127.0.0.1:6379> HGET runoob field2
"World"
```

## list

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

## set

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

## sortedset

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

**redis 单线程为什么快？**  
单线程指的是数据处理为单线程，redis 服务器与客户端通信是 tcp，如果一个请求的数据还没有全部接收到，redis 不会去处理这一部分的数据，这样 redis 就不会因为 io 而被阻塞。单单处理数据的时间是很快的，不会造成长时间的排队或阻塞。单线程还可以避免并发问题。

**Redis 过期**：设 key 的时候有设置过期时间，可以是定期删除过期 key，也可以是获取 key 的时候检查是否过期  
**Redis 置换**：最近最久未使用

## **缓存击穿**

热点数据过期

## **缓存雪崩**

一大批数据同时过期，解决办法：过期时间均匀分布，热点不过期

## **持久机制**

1. 写入 RDB 文件
1. AOF 持久化，把执行过的命令都写入文件，先把命令写在 buf 中，用子进程定时写 aof，并且重写 aof 优化。但会遇到新来的命令修改数据的问题，因此新一个 aofwritebuf，每次重写完再把 aofwritebuf 中的内容写入

# MQ(kafka)

https://zhuanlan.zhihu.com/p/68052232  
https://zhuanlan.zhihu.com/p/74063251

## 卡夫卡集群

**broker**：broker 是 kafka 实例，每个服务器上有一个或多个实例，姑且认为每个服务器上有一个实例，集群中每个 broker 有一个不重复编号  
**Topic**：消息主题，数据保存在 topic 中，每个 broker 可以有多个 topic  
**partition**：topic 的分区，每个 topic 有多个分区，作用是负载，提高吞吐率，每个 topic 在不同分区的数据是不重复的，同一分区内数据是有序的  
**为什么要分区？**：

1. 方便扩展
1. 提高并发，多个消费者可以消费多个分区的消息
   NOTE:只保证某个分区内的消息是顺序消费，不保证跨分区的顺序消费

**replication**：每个分区有多个副本，主分区(leader)故障就备胎(Follower)上位成为 leader。默认副本最大 10 个，副本数量不大于 broker 数量，follower 和 leader 在不同机器，同一个机器对同一个分区只存放一个副本(包括自己)。只有 leader 负责读写，从节点不读写(for consistency)  
**consumer group**：多个消费者组成消费组，同一个分区的数据只能被消费组中的一个消费者消费，同组消费者可以消费同一个 topic 的不同分区的数据(做法是把某一个分区指派给同消费组中的某一个消费者)  
**zookeeper**：kafka 集群依靠 zookeeper 来保存集群的元数据

## 发送数据

1. 生产者从集群获取分区 leader
1. 将消息发给 leader
1. leader 写入本地文件
1. follower 从 leader 那里 pull 消息
1. follower 将消息写完后向 leader 发送 ACK
1. leader 收到所有副本的 ack 后向生产者发 ack

**如何保证消息不丢？**
ack 应答机制。生产者生产时，可以设置参数(0,1,all)确认 kafka 是否收到消息。

1. 0 表示不需要等集群返回，不保证消息发送成功
1. 1 表示 leader 应答就可以发送下一条消息，只确保 leader 保存了
1. all 表示需要所有 follower 都同步完才发下一条

## 保存消息

每个 partition 包括多个 segment 文件，segment 文件中包含 index 文件(存索引)，log 文件(存消息)，文件名表示分段(00000.index 00010.index 表示第一个存了 10 条消息)  
消息结构包括：

1. offset(8b,确定每条消息在 partition 的位置)
1. 消息大小(4b,描述大小)
1. 消息体(被压缩)

不管是否被消费，都会保存所有消息。  
删除策略：基于时间(7 天)，基于大小  
因为 kafka 读取消息的时间复杂度是 O(1)，所以删除消息不提升查找效率

## 消费消息

消费者主动去拉取消息
多个消费者消费不同分区的消息，不会消费同一分区的消息(建议消费者数量与分区数量一致)  
**如何查找 id 为 30006 的消息**
二分查找确定是哪个 segment，index 是稀疏的，也需要二分查找找到比 id 小的最大的索引，最后去 log 中按顺序找  
kafka 会维护每个消费者的 offset
