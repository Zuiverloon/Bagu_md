https://docker-practice.github.io/zh-cn/introduction/what.html

image：包含了所有软件运行的代码或依赖，不可变
container：image 运行时的实体，运行在 runtime 中

## how to pack an image?

使用 dockerfile 定制，写完后 build
1.FROM 指定 base image
2.RUN 在构建 image 的时候执行的指令，如 apt-get install git && apt-get install build-essential
3.COPY 从上下文目录中复制文件进容器中的指定位置
4.EXPOSE 暴露某些端口号
5.CMD 在 docker run 时执行的指令

## 命令

docker run：创建新容器并运行
docker exec：在运行的容器中执行命令
docker start：启动已停止的容器
docker pull：从 dockerhub 中拉取 image
