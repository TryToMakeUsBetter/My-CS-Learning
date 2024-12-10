# 使用
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

select index
```

## 数据类型

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

### String
一个key对应一个val

最大512MB，二进制安全

#### 命令
```redis
SET key value
GET key
INCR key
DECR key
APPEND key value
```

### Hash

#### 命令
```redis
HSET key field value
HGET key field
HGETALL key
HDEL key field
```

### List

#### 命令
```redis
LPUSH key value
RPUSH key value
LPOP key 
RPOP key
LRANGE key start stop
```

### Set

#### 命令
```redis
SADD key value
SREM key value
SMEMBERS key
SISMEMBER key value
```

### ZSet
#### 底层数据结构

#### 命令
```reids
ZADD key socre value
ZRANGE key start stop [withscores]
ZREM key value
ZSCORE key value
```

### HyperLogLog
统计唯一值的近似值

### Bitmaps
位数组，对字符串进行位操作

### Geospatial Indexes
处理地理空间数据，支持空间索引和半径查询

### Streams
日志数据类型

## 事务

Redis命令的执行是原子性的，但是事务不是原子性的
以MULTI开始，用EXEC触发
DISCARD 取消事务

## Redis脚本
Lua解释器执行脚本
# 原理

## 紧凑列表

ziplist-quicklist-listpack

## 跳表
为什么使用跳表而非红黑树，是因为跳表的实现相对来说比较简单

ZSet底层是跳表，键值对存储，key唯一，可排序
为了解决插入引起的索引区间不平衡的问题，引入了随机函数，随机函数生成节点高度

# 面试

## 与mysql的差别
mysql 关系型数据库，持久化数据
redis 非关系型数据库，缓存数据(也可以持久化)

## Redis的优缺点
内存读写速度快
数据结构丰富
支持分布式（主从复制和哨兵机制）
轻量级
可拓展性
支持事务

## redis和mysql的缓存一致问题


## Redis实现消息队列

List
Pub/Sub模式
Stream流