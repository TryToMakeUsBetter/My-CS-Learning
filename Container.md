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
控制资源分配，一旦超出配额，则发出OOM警告，配额包含CPU、内存、磁盘三大方面。

## Docker
REFERENCE
[Docker原理](https://www.51cto.com/article/641494.html)
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

#### chroot和pivot_root
chroot可以改变进程的根目录到指定位置
pivot_root改变当前mount namespace的根目录
挂载在容器根目录上、用来为容器进程提供隔离后执行环境的文件系统就是所谓的容器镜像或者说时rootfs

#### UnionFS
用户制作的每一步镜像都会生成一个层，也就是一个增量rootfs
通过联合挂载，可以使两个文件夹的文件挂在同一个文件下

Docker使用的是AUFS。AUFS支持为每一个成员目录设定不同的读写权限。
1.rw,read-write
2.ro,read-only
3.rr,real-read-only,rr标记的是天生只读分支

使用的联合文件系统，OverlayFS, AUFS, Btrfs, VFS, ZFS 和 Device Mapper。推荐使用 overlay2 存储驱动，overlay2 是目前 Docker 默认的存储驱动，以前则是 AUFS。

#### Docker网络

|网络模式|使用注意|
|:-:|:-:|
|host|和宿主机共享网络|
|none|不配置网络|
|bridge|docker默认|
|container|容器网络互通|

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