# 基本数据结构
## []byte,[]rune,string
string len()返回的是字节个数
[]rune int32的别名存储的是unicode编码，4个字节存储，len()返回的是字符个数
[]byte
### 相互转换
通过内建的函数进行转换
### 相关包
strings

bytes

buffer

binary
## slice
### 扩容
在len-cap之间扩容的时候，不会分配新的地址
大于cap的时候会分配新的地址。
## map与sync.map
map是并发不安全的
有三种解决方案：
    加锁
    sync.map
### map
#### map的基础结构和工作机制
``` go
type hmap struct{
    count int // == len()
    flags uint8 //
    B uint8 //loadFactor * 2 ^ B is the up limit
    noverflow uint16 //overflow buckets
    hash0 uint32 //hash seed

    buckets unsafe.Pointer //bucket in use
    oldbuckets unsafe.Pointer //previous buckets
    nevacuate uintptr

    extra *mapextra
}
```
count代表了map中元素的总个数
B代表要使用的哈希值低位的个数
buckets是使用中的桶
oldbucktes是扩容时原来的桶
noverflow是溢出桶的个数
nevacuate是接下来要迁移的桶的编号
flags判断是否在写入

extra包含了
    overflow 溢出桶的地址数组
    oldoverflow 原桶已使用的溢出桶的地址数组
    nextOverflow 下一个空闲的溢出桶

bucket的结构
```go
type bmap struct{
    tophash [bucketCnt]uint8
    keys [bucketCnt]keytype
    values [bucketCnt]valuetype
    pad uintptr
    overflow uintptr
}
```
bucketCnt大小为8 规定了一个桶存储元素的大小
tophash存储计算出的哈希值的高8位

#### map扩容
#### 触发条件
1. 负载因子超出6.5（可以自己设置），简单理解是桶允许的冗余余量不够了
2. 太多的溢出桶

负载因子（loadFactor）----判断桶是否拥挤

渐进式扩容
当对当前buckets进行写入操作的时候就会出发迁移
 
### sync.Map
#### sync.Map底层结构
``` go
type Map struct {
   mu sync.Mutex

   // read contains .... 省略原版的注释
   // read map是被atomic包托管的，这意味着它本身Load是并发安全的（但是它的Store操作需要锁mu的保护）
   // read map中的entries可以安全地并发更新，但是对于expunged entry，在更新前需要经它unexpunge化并存入dirty
   //（这句话，在Store方法的第一种特殊情况中，使用e.unexpungeLocked处有所体现）
   read atomic.Value // readOnly

   // dirty contains .... 省略原版的注释
   // 关于dirty map必须要在锁mu的保护下，进行操作。它仅仅存储 non-expunged entries
   // 如果一个 expunged entries需要存入dirty，需要先进行unexpunged化处理
   // 如果dirty map是nil的，则对dirty map的写入之前，需要先根据read map对dirty map进行浅拷贝初始化
   dirty map[interface{}]*entry

   // misses counts .... 省略原版的注释
   // 每当读取的是时候，read中不存在，需要去dirty查看，miss自增，到一定程度会触发dirty=>read升级转储
   // 升级完毕之后，dirty置空 &miss清零 &read.amended置false
   misses int
}

// 这是一个被原子包atomic.Value托管了的结构，内部仍然是一个map[interface{}]*entry
// 以及一个amended标记位，如果为真，则说明dirty中存在新增的key，还没升级转储，不存在于read中
type readOnly struct {
   m       map[interface{}]*entry
   amended bool // true if the dirty map contains some key not in m.
}

// An entry is a slot in the map corresponding to a particular key.
// 这是一个容器，可以存储任意的东西，因为成员p是unsafe.Pointer(*interface{})
// sync.Map中的值都不是直接存入map的，都是在entry的包裹下存入的
type entry struct {
   // p points ....  省略原版的注释
   // entry的p可能的状态：
   // e.p == nil：entry已经被标记删除，不过此时还未经过read=>dirty重塑，此时可能仍然属于dirty（如果dirty非nil）
   // e.p == expunged：entry已经被标记删除，经过read=>dirty重塑，不属于dirty，仅仅属于read，下一次dirty=>read升级，会被彻底清理
   // e.p == 普通指针：此时entry是一个不同的存在状态，属于read，如果dirty非nil，也属于dirty
   p unsafe.Pointer // *interface{}
}
```
分列两张Map，一张是read，仅用与读；
另一个Map是dirty，用于在特定情况下存储最新写入的key-value
包含一个misses字段，该字段表明read被穿透的次数

#### 适用场景

读多写少

原因：read一直同步dirty和实际上频繁的穿透read导致的加锁性能损耗。

## 接口
最大的用处
    鸭子类型
## 函数
函数可以独立于结构体存在，这有助于实现函数式编程
## 结构体
## chan
# GC
三色标记法
## 工作流程
标记准备阶段：启用写屏障
标记阶段：扫描堆栈的根结点，将第一趟扫描所得的节点，加入到灰色节点合集；从灰色节点合集选一个节点，遍历其子节点；所有被扫描到的子节点，标记成灰色加入到灰色节点合集；将被扫描子节点的节点标记为黑色。重复知道不存在灰色节点。
标记结束阶段：关闭写屏障，并计算下一次清理的目标和计划。
清理阶段：回收白色节点。
## 三色不变性
强三色不变性：黑色对象不会引用白色对象。
弱三色不变性：黑色对象可以引用白色对象，存在其他灰色对象引用他
## 改良
### 保障三色不变性
删除屏障：在删除引用关系的时候，若对象是灰色或者白色，则将其标志为灰色。
插入屏障：在插入新的引用的时候若被插入的对象是白色就标记成灰色。插入屏障只应用于堆内存空间而非栈内存空间
### 优化STW
混合写屏障：在GC开始的时候，把删除的对象标记成灰色，GC过程中，应用插入屏障逻辑。
# 协程
## 协程、进程和线程
### 概念
进程是资源分配的基本单位，有独立的内存空间和系统资源，是一个独立运行的程序实例，进程之间相互隔离。
线程是CPU调度的基本单位，是进程中的一个执行路径，一个进程内的线程共享进程的内存空间和资源。但是每个线程有自己的栈和寄存器。
协程：是用户态的调度单位，协程是用户态的调度单位，可以在单线程中实现并发。协程不通过操作系统调度。
### 调度
进程：操作系统内核进行调度，切换时需要保存和恢复所有的CPU状态和内存空间。
线程：操作系统进行调度，只需要保存和恢复CPU寄存器和栈指针。
协程：无需操作系统，切换时只需保存和恢复少量的上下文信息。
### 通信
进程： 管道、消息队列、共享内存、信号量、信号、Socket
线程： 共享内存、互斥锁、条件变量、信号量、事件
协程： 全局变量或者是消息传递机制
## Goroutine
优势：栈空间最小可以到2KB，线程的栈空间最小是2MB
## 调度
GMP模型
G means Groutine
M means Physical Processor
P means Logical Processor
