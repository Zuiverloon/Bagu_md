# MQ(kafka)

https://zhuanlan.zhihu.com/p/68052232  
https://zhuanlan.zhihu.com/p/74063251

## 卡夫卡集群

用途：实时 data pipeline，解藕微服务，immutable log，流量削峰

**message**:包括 header(topic&partition)，key value
**broker**：broker 是 kafka 实例，每个服务器上有一个或多个实例，姑且认为每个服务器上有一个实例，集群中每个 broker 有一个不重复编号  
**Topic**：消息主题，数据保存在 topic 中，每个 broker 可以有多个 topic  
**partition**：topic 的分区，每个 topic 有多个分区，作用是负载，提高吞吐率，每个 topic 在不同分区的数据是不重复的，同一分区内数据是有序的  
**为什么要分区？**：

1. 方便扩展
1. 提高并发，多个消费者可以消费多个分区的消息
   NOTE:只保证某个分区内的消息是顺序消费，不保证跨分区的顺序消费

**replication**：每个分区有多个副本，主分区(leader)故障就备胎(Follower)上位成为 leader。默认副本最大 10 个，副本数量不大于 broker 数量，follower 和 leader 在不同机器，同一个机器对同一个分区只存放一个副本(包括自己)。只有 leader 负责读写，从节点不读写(for consistency)  
**consumer group**：多个消费者组成消费组，同一个分区的数据只能被消费组中的一个消费者消费，同组消费者可以消费同一个 topic 的不同分区的数据(做法是把某一个分区指派给同消费组中的某一个消费者)  
**zookeeper**：旧版本 kafka 集群依靠 zookeeper 来保存集群的元数据
**Kraft**:新版本中使用 kraft 共识算法

## 发送数据

1. 生产者从集群获取 broker leader
1. 将消息发给 leader
1. leader 写入本地文件

如果有应答机制

4. follower 从 leader 那里 pull 消息
5. follower 将消息写完后向 leader 发送 ACK
6. leader 收到所有副本的 ack 后向生产者发 ack

**如何保证消息不丢？**
ack 应答机制。生产者生产时，可以设置参数(0,1,all)确认 kafka 是否收到消息。

1. 0 表示不需要等集群返回，不保证消息发送成功
1. 1 表示 leader 应答就可以发送下一条消息，只确保 leader 保存了
1. all 表示需要所有 follower 都同步完才发下一条

## 保存消息

采用分区日志系统
每个 partition 包括多个 segment 文件，segment 文件中包含 index 文件(存索引，但不是每条偏移量都存，而是每过一定字节数，记录一个)，log 文件(存消息)，文件名表示分段(00000.index 00010.index 表示第一个存了 10 条消息)  
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

### 如果消费者离开，发生什么？

rebalance：把分区重新分配给所有消费者，当新的消费者消费某个 partition 时，会从最新的 offset 开始防止重复

## 为什么 kafka 不是推消息给消费者

速率难控制，容易消息堆积/消费者崩溃  
状态维护复杂：需要跟踪每个消费者的状态

### Producer Configuration Properties

```
bootstrap.servers	// List of Kafka broker addresses
acks	// Message durability level (0, 1, or all)

```

### Consumer Configuration Properties

```
group.id	// Consumer group identifier
auto.offset.reset	// Behavior when no offset is found (earliest, latest,none(if no offset, give error)), if already having offset, then start from offset
enable.auto.commit	// Whether to auto-commit offsets
max.poll.records	// Maximum number of records returned in a single poll
key.deserializer / value.deserializer	// Classes to deserialize keys and values


```

### common command

```shell
# create topic
bin/kafka-topics.sh --bootstrap-server <broker> --create --topic <topic-name> --partitions 3 --replication-factor 2

# Send messages to a topic
bin/kafka-console-producer.sh --broker-list <broker> --topic <topic-name>

# Consume messages from a topic
bin/kafka-console-consumer.sh --bootstrap-server <broker> --topic <topic-name> --from-beginning



```

## kafka mirror

## 如何保证不重复消费

1. 幂等生产者（Idempotent Producer）启用 enable.idempotence=true，kafka 为每条消息分配唯一序列号，broker 做去重，确保消息不被重复写入
2. 事务性生产者消费者（Transactional Messaging），生产者将一组消息以一个事务写入 kafka，消费者消费一组事务
3. 手动提交偏移量，消息处理成功再 commit，避免消费者崩溃导致的重复消费
4. 外部存储偏移量而不是 kafka 内部存储
5. 消费端做幂等，使用消息 ID 判断是否已经处理

## broker 宕机的影响

对生产者：数据可能丢失，除非消息同步到所有副本（ack=all）  
对消费者：消费者只能从 leader 读取，leader 宕机了会选举新的 leader，消费者会重连，没有副本选为 leader 就停止消费

## kafka 协议

kafka 在客户端/broker 通信时，使用的是基于 TCP 的自定义二进制协议，称为 kafka 协议
