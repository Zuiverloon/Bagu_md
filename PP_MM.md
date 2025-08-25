# Merchant Monitor

## CALBIZ to BQ

4B records every day
50k per second in 50 machines

## 项目难点

## 发现 bug 的经历

kryo 序列化器不是 thread safe 的
在测试 kafkaproducer 多线程发消息和 consumer 消费消息的时候，发现 consumer 收到的消息格式和预期的不一致。check producer 发送的消息的格式，发现多个线程访问一个序列化器，会导致序列化器的内容被覆盖，需要用 threadlocal 解决，

## STAR 方式回答问题

• S = Situation: Briefly describe the context. What was happening?
• T = Task: What was your responsibility or goal?
• A = Action: What steps did you take to solve the problem?
• R = Result: What was the outcome? Try to quantify it if possible.
