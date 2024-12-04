# 数据库使用
## 以MySQL、Innodb为例
### 基础的使用
``` sql
Link 
    mysql -u user_name -p password
DB
    show databases
    use <db_name>
    create database <db_name>
    drop database <db_name>
TABLE
    show tables
    describe <table_name>;show columns from <table_name>
    create table <table_name>(<column_name1> <type> [restrict]......)
    drop table
Column
    insert into <table_name> (<col0>,<col1>) values(<val0>,<val1>)
    select <col1>,<col2> from <table_name> where <cond>
    delete from <table_name> where <cond>
User
    create user <'user_name'@'host'> identified by <'pwd'>
    grant <privileges_kind> on <db_name.*> TO <'user_name'@'host'>
    flush privileges
    select user()
Exit

Transaction
    start transaction ; begin
    commit
    rollback
    show engine innodb status
    lock tables <table_name> write|read
    unlock tables
    set transaction isolation level read uncommitted|read committed|repeatable read|serializable
```
### 表的连接
``` sql
    select <col> from tablea a inner join tableb b on a.key = b.key
    select <col> from tablea a left join tableb b on a.key = b.key
    select <col> from tablea a right join tableb b on a.key = b.key
    select <col> from tablea a left join tableb b on a.key = b.key where <b.key is NULL|a.key is NULL>
    select <col> from tablea a full outer join tableb b on a.key = b.key 
```

### 设计范式
#### BCNF
在3NF上进一步规范，在其他列属性不是主键的子集的情况下，构建超键
#### 1NF
数据库中表的每一列都是不可分割的基本数据项，同一列中不可以有多个值。
#### 2NF
每个实例或行必须可以被唯一地区分，要求实体的属性完全依赖于主关键字。主键约束，消除部分依赖。
#### 3NF
要求一个数据库表中不包含已在其他表中已包含的非主关键字信息。外键约束，消除传递依赖。

### 底层
#### Innodb
[REFERENCE](https://www.zhihu.com/question/594498434/answer/3004627072)

#### 数据库行的底层结构
#### 并发控制
##### 锁
表锁

临键锁

###### 行级锁
在查询语句以后增加一个FOR UPDATE(InnoDB)

###### 间隙锁
一个区间的锁，是开区间

共享锁/排他锁

意向共享锁/意向排他锁

插入意向锁

自增锁
#### 事务

##### 隔离级别

Read Uncommitted

Read Committed

Repeatable Read

Serializable

##### 事务日志

存储引擎只需要修改内存中的数据副本，将更改的记录写入事务日志，事务日志会被持久化到硬盘。

##### AUTOCOMMIT
单个insert、update或delete会被包装在一个事务中提交
不要在事务中混用不同的存储引擎

##### 隐式锁定和显式锁定
Innodb----两阶段锁定协议

事务执行期间随时可以获取锁

锁在提交或回滚后才会释放（所有锁同时释放）
#### MVCC
通过Read View来实现
每行记录的结构除了我们定义的内容，还包括数据库隐式定义的DB_TRX_ID,DB_ROLL_PTR,DB_ROW_ID等字段

DB_ROW_ID,隐含的6Byte自增ID，（Innodb）如果没有主键，则会自动以此生成一个聚簇索引

DB_TRX_ID,6Byte,最近修改的事务ID

DB_ROLL_PTR,回滚指针,指向undo log中的上一个版本

DELETED_BIT,删除不代表真的删除，只是置为删除。

##### undo log
Insert Undo log 插入对应的就是删除，所以不需要保存数据

Update Undo log 修改则需要保留旧有的值，并且使用DB_ROLL_PTR指向该记录

Delete Undo log 对应的是插入，需要记录旧有的值，这也是为什么前文所述的删除，并不是真的删除，因为需要保留DB_ROLL_PTR来指向旧地址

##### Read View

对一个事务执行快照读的时候，会生成一个Read View。
Read View维护的属性:
    trx_list,Read View被创建的时候活跃的事务列表
    up_limit_id,trx_list中最小的事务id
    low_limit_id,生成时刻系统尚未分配的下一个事务的ID
如果DB_TRX_ID < up_limit_id 则当前事务可以看见该记录，如果DB_TRX_ID > low_limit_id则不可见。如果在二者之间，则查找trx_list如果在其中则不可见，不在其中则可见

### 索引
#### 常见索引模型
##### 哈希表
##### 有序数组
##### 二叉搜索树
特点:父节点左子树所有节点比父节点小，右子树所有节点比父节点大。
##### InnoDB索引模型
B+树
1. 减少I/O次数，叶子节点可以形成一个列表，数据叶中可存放的有效数据增加了。
2. 每次查询的时间复杂度固定
3. 遍历效率更高(范围查询)

B树


#### 索引分类
##### 聚簇索引
使用主键构造一棵B+树，叶子节点存放整张表的行数据。

没有主键就使用，其他可以成为主键的列，没有这样的列就使用数据库内置的值
##### 非聚簇索引
使用多个字段建立的索引

最左匹配原则，当where语句中用到了联合索引中最左边的索引，就会使用联合索引进行匹配，当遇到(<,>,between,like)就会终止匹配。

比如index(name,age,phone_number)

##### 前缀索引
当索引列的字符比较多的时候，可以只索引列开始的部分字符串，节约索引空间，提高效率。

比如index(key(number))

##### 普通索引
普通索引的叶子节点不存放行数据，而是主键值。

##### 唯一索引
唯一索引允许有空值

##### 全文索引
允许在char、varchar上建立全文索引

### 视图

``` go
create view <view_name> as <select语句>
```
# 容器化部署
``` cmd
docker run -p 3306:3306 --name JY_mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```
