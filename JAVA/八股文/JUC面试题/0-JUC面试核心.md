# JUC (Java Util Concurrent) 面试核心

Java 并发编程是 Java 高级开发的核心能力，也是大厂面试的重点。

## 目录
- [[1-JUC基础]] - 线程状态、创建方式、常用方法
- [[2-JMM]] - 原子性、可见性、有序性、volatile、happens-before
- [[3-锁]] - synchronized, ReentrantLock, 读写锁, 乐观锁
- [[4-AQS核心原理]] - AbstractQueuedSynchronizer 核心原理深度剖析
- [[5-线程池]] - ThreadPoolExecutor 参数、拒绝策略、执行流程
- [[6-ThreadLocal]] - 变量隔离机制、内存泄漏预防
- [[7-并发工具类]] - CountDownLatch, CyclicBarrier, Semaphore

## 高频考点
- 线程池参数：corePoolSize, maximumPoolSize, keepAliveTime, workQueue...
- AQS 原理：State 状态, CLH 队列, 独占/共享模式
- synchronized 升级：无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁
- CAS 与 ABA 问题：Atomic 系列类, 版本号机制
- 内存屏障与 happens-before：JMM 的底层保证
