# Docker And Containerd
## 原理机制
### 如何实现的隔离
CGroup和Namespace
#### Namespace
Namespace可以隔离进程ID、主机名、用户ID、文件名、网络访问和进程间通信等相关资源。
mnt namespace:文件系统挂载点隔离

net namespace:隔离网络接口

pid namespace:隔离进程ID

ipc namespace:信号量、消息队列和共享内存的隔离

uts namespace:主机名和域名的隔离

user namespace:用户和用户组的隔离

#### CGroup
控制资源分配，一旦超出配额，则发出OOM警告，配额包含CPU、内存、磁盘三大方面
## Docker
### Dockerfile
``` Dockerfile
FROM 指定基础镜像
RUN 在构建过程中执行的命令
LABEL KV形式添加元数据
CMD 指定容器创建时的默认命令(可以被覆盖)
ENTRYPOINT 设置容器创建时的主要命令(不可被覆盖)
EXPOSE 声明容器运行时监听的特定网络端口
ENV 在容器内部设置环境变量
ADD 将文件、目录或者远程URL复制到镜像中
COPY 将文件或者目录复制到镜像中
VOLUME 为容器创建挂载点或声明卷
WORKDIR 设置后续指令的工作目录
USER 指定后续指令的用户上下文
ARG 定义在构建过程中传递给构建器的变量
ONBUILD 当该镜像作为另一个构建过程的基础时，添加的触发器
STOPSIGNAL 设置发送给容器以退出的系统调用信号
HEALTHCHECK 定义周期性检查容器健康状态的命令
SHELL 覆盖默认shell
```

### 组件

dockerd负责响应和处理来自docker客户端的请求
#### Docker网络

### 使用
``` shell
运行交互式容器
docker run -i -t ubuntu:15.10 /bin/bash

启动容器(后台模式)
docker run -d xxxxxx /bin/bash -c xxxxx

查看容器
docker ps

查看日志
docker logs

停止容器
docker stop

查看镜像
docker images   

拉取新镜像
docker pull xxxx

查找镜像
docekr search xxx

删除镜像
docker rmi 

构建镜像
docker build

网络端口映射
docker run -d -p 5000:5000 

命名
docker run --name xxxx

制定目录映射
docker run -v 
```
https://s4.51cto.com/oss/202101/18/c2703afb72dc96bdcc3e7882d547acac.png

### 容器的状态

1. created

2. restarting

3. running

4. removing

5. paused

6. exited

7. dead