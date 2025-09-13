# Merchant Monitor

## infra

jdk 17
springboot 2.7
spring-kafka 2.9 (kafka 3.2)
react17
0.9 core，8GB RAM
50%cpu utilization，30% MEM utilization

## merchant monitoring

consume merchant api log, extract api activity details like merchant account, api name, response time, response code, and ingest into several databases, like bq, druid, storm cluster.
front end, we allow user to input a merchant acc and a period of time, we show merchant api activty in a line chart widget to show the trend of api calls, and a table widget listing all apis a merchant called, the number of calls o each api, how many 500, how many 200, and if user rqquired, will search corr id from bigquery.

## merchant observability(available for whitelisted merchant/partners)

provide extra observability feature for whitelisted merchants like shopify/google.
show data for paypal feature(how many new account onboarded, dispute, negative payout happened)
fetch live data from datadog, or periodically run cron jobs to fetch metrics/counters.

## CALBIZ to BQ

4B records every day
50k per second in 50 machines

## druid 优点

时序数据库  
支持 SQL/JSON  
实时性高  
聚合分组查询效率高  
列存储  
预聚合：摄入时对相同维度数据进行聚合，减少存储量
倒排索引

同类的有 clickhouse，但是实时性不如 druid
用 tranquilizer 发送单条数据，自动处理分片，支持异步、重试、回调
用 curator 当作 zookeeper

## 项目难点

需要将 log 按分钟维度做聚合，发到一个 kafka topic，作为 alerting 的数据源。消费上游的时候相同 account，api 可能在不同的机器上，为了聚合需要把他们发到一个机器上。
引入了一个内部 topic，相同 api account 用一样的 key，专门做聚合。
还有一个消息乱序的问题，可能前三分钟的消息现在才到，所以会维护一个时间窗口，大概是 3 分钟，将聚合结果保留在内存里 3 分钟，等三分钟再发送到下游，告警也会有 3 分钟的延时。每分钟有一个线程异步发送一个窗口内的所有数据

## 发现 bug 的经历

kryo 序列化器不是 thread safe 的
在测试 kafkaproducer 多线程发消息和 consumer 消费消息的时候，发现 consumer 收到的消息格式和预期的不一致。check producer 发送的消息的格式，发现多个线程访问一个序列化器，会导致序列化器的内容被覆盖，需要用 threadlocal 解决，

## STAR 方式回答问题

• S = Situation: Briefly describe the context. What was happening?
• T = Task: What was your responsibility or goal?
• A = Action: What steps did you take to solve the problem?
• R = Result: What was the outcome? Try to quantify it if possible.

## druid

支持 sql
