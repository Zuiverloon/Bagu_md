## Redis

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
