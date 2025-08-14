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
