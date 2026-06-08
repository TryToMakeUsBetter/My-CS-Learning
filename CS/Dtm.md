# TCC事务

## 概述
Try-Confirm-Cancel 三段式

Try 占用资源
Confirm 使用资源
Cancel 失败补偿

我的理解是Try上锁抢资源，Confirm是真正业务的执行，Cancel做失败补偿

## 场景

余额
Try表负责记录被扣减/增加的金额,由于Confirm会多次执行所以Confirm接口需要做幂等设计
Try接口:
对于扣减操作Try表需要记录的是Try阶段扣减了多少,同时扣减余额表
对于增加操作Try表需要记录的是Confirm阶段预充值了多少,由于Confirm会执行多次
Confirm接口:
对于扣减Confirm不做创建
对于增加则会执行真正的余额操作
Cancel:
扣减 -> 加
加 -> 扣减