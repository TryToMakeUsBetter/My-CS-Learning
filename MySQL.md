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
