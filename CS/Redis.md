# 使用

[面试 Redis 没底？这 40 道面试题让你不再慌（附答案）-阿里云开发者社区](https://developer.aliyun.com/article/852976)

## 布隆过滤器

## 基础

连接:redis-cli

退出:exit

16个库，0库和1库不可以修改:select 1

查看所有的key值 keys *

``` redis
auth password

echo message

ping

quit

select index //选择库
```

### 数据类型

string：可以存储字符串、整数或浮点数

hash：一个键值对集合

list：一个简单的列表，存储一系列字符串

set：一个无序合集，存储不重复的字符串

zset：有序集合

bitmap：基于字符串类型，可以对每个位进行操作

hyperloglogs：基于基数统计，可以估算集合中唯一元素数量

geospatial：存储地理位置信息

pub/sub：消息通信模式，允许客户端订阅消息通道，并接收发布到该通道的消息

streams：用于消息队列和日志存储，支持消息的持久化和时间排序

modules：拓展Redis功能

#### String

一个key对应一个val

最大512MB，二进制安全

命令

```redis
SET key value
GET key
DEL key
INCR key
DECR key
APPEND key value
```

#### Hash

命令

```redis
HSET key field value
HGET key field
HGETALL key
HDEL key field
```

#### List

命令

```redis
LPUSH key value
RPUSH key value
LPOP key 
RPOP key
LRANGE key start stop
```

#### Set

命令

```redis
SADD key value
SREM key value
SMEMBERS key
SISMEMBER key value
```

#### ZSet

底层数据结构

命令

```reids
ZADD key socre value
ZRANGE key start stop [withscores]
ZREM key value
ZSCORE key value
```

#### HyperLogLog

统计唯一值的近似值

#### Bitmaps

位数组，对字符串进行位操作

#### Geospatial Indexes

处理地理空间数据，支持空间索引和半径查询

#### Streams

日志数据类型

### 事务

Redis命令的执行是原子性的，但是事务不是原子性的
以MULTI开始，用EXEC触发
DISCARD 取消事务

### Redis脚本

Lua解释器执行脚本

### 持久化方式

RDB和AOF方式

RDB（Redis DataBase）持久化方式：是指用数据集快照的方式半持久化模式记录 Redis 数据库的所有键值对，在某个时间点将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次持久化的文件，达到数据恢复。

优点：

- 只有一个文件 dump.rdb，方便持久化。
- 容灾性好，一个文件可以保存到安全的磁盘。
- 性能最大化，fork 子进程来完成写操作，让主进程继续处理命令，所以是 IO 最大化。使用单独子进程来进行持久化，主进程不会进行任何 IO 操作，保证了 Redis的高性能。
- 相对于数据集大时，比 AOF 的启动效率更高。

缺点：数据安全性低。RDB 是间隔一段时间进行持久化，如果持久化之间 Redis 发生故障，会发生数据丢失。所以这种方式更适合数据要求不严谨的时候

AOF（Append-only file）持久化方式：是指所有的命令行记录以 Redis 命令请求协议的格式完全持久化存储保存为 aof 文件。

优点：

- 数据安全，aof 持久化可以配置 appendfsync 属性，有 **always**，每进行一次命令操作就记录到 aof 文件中一次。
- 通过 append 模式写文件，即使中途服务器宕机，可以通过 **redis-check-aof** 工具解决数据一致性问题。
- AOF 机制的 rewrite 模式。AOF 文件没被 rewrite 之前（文件过大时会对命令进行合并重写），可以删除其中的某些命令（比如误操作的 flushall）

缺点：

- AOF 文件比 RDB 文件大，且恢复速度慢。
- 数据集大的时候，比 RDB 启动效率低。

## 原理

### 紧凑列表

ziplist-quicklist-listpack

### 跳表

为什么使用跳表而非红黑树，是因为跳表的实现相对来说比较简单

ZSet底层是跳表，键值对存储，key唯一，可排序
为了解决插入引起的索引区间不平衡的问题，引入了随机函数，随机函数生成节点高度

## 面试

### 与mysql的差别

mysql 关系型数据库，持久化数据
redis 非关系型数据库，缓存数据(也可以持久化)

### Redis的优缺点

内存读写速度快
数据结构丰富
支持分布式（主从复制和哨兵机制）
轻量级
可拓展性
支持事务

### Redis实现消息队列

List
Pub/Sub模式
Stream流

### Redis常见的性能问题

Master 最好不要写内存快照，如果 Master 写内存快照，save 命令调度 rdbSave函数，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务。

如果数据比较重要，某个 Slave 开启 AOF 备份数据，策略设置为每秒同步一次。

为了主从复制的速度和连接的稳定性，Master 和 Slave 最好在同一个局域网。

尽量避免在压力很大的主库上增加从。

主从复制不要用图状结构，用单向链表结构更为稳定，即：Master <- Slave1<- Slave2 <- Slave3……这样的结构方便解决单点故障问题，实现 Slave 对 Master 的替换。如果 Master 挂了，可以立刻启用 Slave1 做 Master，其他不变。

### Redis过期键删除策略