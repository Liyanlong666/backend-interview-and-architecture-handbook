# Redis

Redis 是一个高性能的键值对数据库。

## 目录
- [[Redis基础]] - 基本概念、数据结构
- [[底层结构/底层结构]] - SDS、ziplist、skiplist 等实现原理
- [[持久化/持久化]] - RDB、AOF、混合持久化
- [[缓存三件套/缓存三件套]] - 缓存雪崩、穿透、击穿
- [[Redis 分布式锁/Redis 分布式锁]] - 实现方案与 Redlock
- [[分布式锁常见实现方案总结  JavaGuide/分布式锁常见实现方案总结  JavaGuide]] - 综合对比

## 高频考点
- 数据结构：String, Hash, List, Set, ZSet
- 线程模型：单线程为什么快？(IO多路复用)
- 淘汰策略：LRU, LFU, Random 等
- 集群方案：主从、哨兵、Cluster
