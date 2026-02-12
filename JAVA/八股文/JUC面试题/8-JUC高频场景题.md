# JUC 高频场景题汇总

本文汇总了常见的 JUC 并发编程场景面试题，涵盖锁、线程池、AQS、并发工具类等核心知识点。


## 3-锁

### [43. 常见场景题汇总]
#### [场景1：分布式环境下的锁失效]
**问题描述**：
某服务部署在多台服务器上（分布式/集群环境），为了保证商品库存不超卖，开发者在扣减库存的方法上使用了 `synchronized` 关键字。这样做有效吗？为什么？

**解答**：
无效。
`synchronized` 和 `ReentrantLock` 都是 JVM 级别的锁，只能保证同一台服务器（同一个 JVM 进程）内的线程安全。在分布式环境下，不同服务器的线程属于不同的 JVM，本地锁无法互斥。
**解决方案**：
需要使用**分布式锁**，如：
1.  **Redis 分布式锁**：使用 `setnx` + `lua脚本`（Redisson 框架）。
2.  **Zookeeper 分布式锁**：利用临时顺序节点。
3.  **数据库悲观锁**：`select ... for update`。

#### [场景2：死锁排查与解决]
**问题描述**：
线上系统突然变慢，某些功能卡死。通过 `jstack` 发现两个线程互相持有对方需要的锁（Thread A hold Lock1 wait Lock2; Thread B hold Lock2 wait Lock1）。如何解决？

**解答**：
这是典型的死锁。
1.  **恢复服务**：重启服务（短期止损）。
2.  **代码修复**：
    *   **固定加锁顺序**：确保所有线程都按照 Lock1 -> Lock2 的顺序加锁。
    *   **使用 tryLock**：使用 `ReentrantLock.tryLock(timeout)`，获取失败则释放已持有的锁并重试，避免死等。
    *   **资源排序**：对需要获取的资源（如数据库行 ID）进行排序，按顺序获取。

#### [场景3：高并发计数器（CAS 自旋瓶颈）]
**问题描述**：
你需要实现一个网站的访问量计数器，QPS 很高（如 10w+）。使用 `AtomicInteger` 安全吗？会有什么性能问题？

**解答**：
`AtomicInteger` 是线程安全的（基于 CAS），但在高并发激烈竞争下：
1.  **性能问题**：大量线程同时 CAS 失败并自旋（空转 CPU），导致 CPU 使用率飙升。
2.  **解决方案**：
    *   **LongAdder**：JDK 8 引入，使用分段 CAS（Cell 数组），减少竞争，吞吐量远高于 `AtomicInteger`。
    *   **Redis Incr**：利用 Redis 单线程原子性，抗并发能力更强，且支持持久化。

#### [场景4：读多写少的缓存保护]
**问题描述**：
有一个本地配置缓存，读取请求占 99%，只有极少情况会更新配置。使用 `synchronized` 保护会有什么问题？

**解答**：
`synchronized` 是独占锁，读读也会互斥，导致读并发能力低下。
**解决方案**：
使用 **`ReentrantReadWriteLock`**（读写锁）。
*   **读锁（Shared）**：多线程可同时持有，互不阻塞。
*   **写锁（Exclusive）**：写时阻塞所有读写。
*   **进一步优化**：JDK 8 的 **`StampedLock`**，提供乐观读模式，性能更好（但使用复杂，不可重入）。

#### [场景5：生产者-消费者模型的精准唤醒]
**问题描述**：
实现一个阻塞队列，要求当队列满时，生产者线程阻塞；队列空时，消费者线程阻塞。且要求**生产者只唤醒消费者，消费者只唤醒生产者**（避免无效唤醒同类线程）。

**解答**：
使用 `ReentrantLock` + `Condition`。
1.  定义两个 Condition：`notEmpty`（非空条件）和 `notFull`（未满条件）。
2.  **生产者**：
    *   获取锁。
    *   `while (queue.full()) notFull.await();`
    *   入队。
    *   `notEmpty.signal();` (只唤醒等待的消费者)
3.  **消费者**：
    *   获取锁。
    *   `while (queue.empty()) notEmpty.await();`
    *   出队。
    *   `notFull.signal();` (只唤醒等待的生产者)
对比 `synchronized` 的 `notifyAll()` 会唤醒所有线程（包括同类），`Condition` 效率更高。

## 4-AQS核心原理

### 常见场景题汇总

#### [场景1：自定义同步器设计]
**问题描述**：
业务场景需要一个“两门闩”同步器（Binary Latch）：门闩只有开启和关闭两种状态。当门闩关闭时，所有尝试获取的线程都会阻塞；当门闩开启时，所有线程放行。与 `CountDownLatch` 不同的是，这个门闩可以被重复关闭和开启。请问如何基于 AQS 设计？

**解答**：
1.  **定义 State**：`state=0` 表示关闭（阻塞），`state=1` 表示开启（放行）。
2.  **实现 tryAcquireShared**：
    *   如果 `state == 1`，返回 1（成功）。
    *   如果 `state == 0`，返回 -1（失败，进入等待队列）。
3.  **实现 tryReleaseShared**：
    *   设置 `state = 1`，并唤醒所有等待线程。
    *   提供额外方法 `close()` 设置 `state = 0`。
这是 AQS **共享模式**（Shared）的典型应用。

#### [场景2：AQS 资源状态含义辨析]
**问题描述**：
面试官问：AQS 中的 `state` 变量在 `ReentrantLock`、`CountDownLatch` 和 `Semaphore` 中分别代表什么含义？如果让你实现一个类似于“王者荣耀五人组队”的逻辑（满5人才能开始），你会怎么用？

**解答**：
*   **含义**：
    *   `ReentrantLock`：`state` = 锁重入次数（0表示无锁，1表示持有，>1表示重入）。
    *   `CountDownLatch`：`state` = 剩余需要倒数的数量（计数器）。
    *   `Semaphore`：`state` = 剩余可用许可证数量。
*   **场景实现（五人组队）**：
    *   使用 `CyclicBarrier`（基于 ReentrantLock + Condition）或 `CountDownLatch`。
    *   若用 `CountDownLatch`：设置 `state = 5`。每个玩家准备好调用 `countDown()`。主线程调用 `await()` 等待 `state` 变为 0 后开始游戏。

#### [场景3：排查线程堆积]
**问题描述**：
系统出现卡顿，怀疑是有大量线程在等待某把锁。除了 dump 线程栈外，如何在代码运行期实时监控这把锁的等待队列长度？

**解答**：
AQS（及其子类如 ReentrantLock）提供了监控方法（通常是 `final` 或 `public` 的）：
1.  **`getQueueLength()`**：返回等待队列中的估计线程数。
2.  **`hasQueuedThreads()`**：判断是否有线程在等待。
3.  **`getOwner()`**（protected）：获取持有当前锁的线程（用于排查是谁拿了锁不释放）。
在业务监控代码中，可以定时打印 `lock.getQueueLength()` 来触发报警。
