# NSQ

## backoff 

指数退避机制: 连续失败次数为n，全局重试延迟2^n seconds以后执行重试,连续失败以后出现连续成功次数为m次,重试延迟时间为2^(n-m) seconds, m最大为n