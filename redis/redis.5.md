# 分布式锁的注意事项

- 互斥性：在任意时刻，只有一个客户端能持有锁
- 同一性：加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。
- 可重入性：即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁

# 实现分布式锁

## 获取锁

## 释放锁