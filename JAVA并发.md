[TOC]

# JAVA并发

## 核心内容

- 线程池
- synchronized
- volatile
- ThreadLocal
- **AQS**：问得少

## 重点

- 线程池参数与执行流程
- synchronized 原理
- volatile
- ThreadLocal

## 1. 线程创建方式

1. 继承Thread
2. 实现Runnable（无返回值）
3. 实现Callable（有返回值，配合 FutureTask）
4. 线程池

## 2. 线程池 7 参数

- **corePoolSize**：核心线程数
- **maxPoolSize**：最大线程数
- **keepAliveTime**：空闲存活时间
- **unit**：时间单位
- **workQueue**：任务队列
- **threadFactory**：线程工厂
- **handler**：拒绝策略

### 执行流程

1. 核心线程
2. 队列
3. 最大线程
4. 拒绝策略

## 3. 4 种拒绝策略

- **AbortPolicy**：抛异常（默认）
  
- **CallerRunsPolicy**：提交任务的线程自己执行
- **DiscardPolicy**：直接丢弃
- **DiscardOldestPolicy**：丢弃队列头任务

## 4. synchronized 原理

- 作用于实例方法、静态方法、代码块
- 锁升级

1. 无锁
2. 偏向锁
3. 轻量级锁
4. 重量级锁

- **底层**：monitorenter / monitorexit 指令

## 5. volatile 作用

- **可见性**：强制刷新到主内存
  
- **禁止指令重排**：通过内存屏障实现
  
- **不保证原子性**

## 6. ThreadLocal

- 每个线程独立副本，线程隔离
- **数据结构**：ThreadLocalMap
  - Entry 的 key 是弱引用

> ❗️内存泄露
>
> - 当 key 被 GC 回收变为 null 后，该 Entry 的 value 仍然存在（强引用）
> - 如果当前线程一直存活（如线程池中的线程），这个 value 永远无法被访问到，导致内存泄露
> - **解决方法**：用完后必须调用`remove()`

## 7. sleep VS wait

| 区别   | sleep            | wait               |
| ------ | ---------------- | ------------------ |
| 所属   | Thread 静态方法  | Object 方法        |
| 释放锁 | ❌️                | ✅️                  |
| 唤醒   | 自动 / interrupt | notify / notifyAll |

## 追问

### ==1. 线程池补充==

**corePoolSize 和 maxPoolSize 什么时候会用到 max？**

- 当核心栈都在忙，且==队列满了，才会创建新线程==直到 maxPoolSize

 **workQueue 常见类型及影响**

- **LinkedBlockingQueue（无界）**：任务可无限排队，永远不创非核心线程
  
- **ArrayBlockingQueue（有界）**：队列满后触发新线程
  
- **SynchronousQueue（0 容量）**：直接提交，立即创线程

**拒绝策略适用场景**

- CallerRunsPolicy
  - 适合对延迟不敏感、不允许丢弃的任务
    - 主线程帮忙执行，压住生产者

- DiscardOldestPolicy
  - 适合时效性高的任务，丢弃最旧的，尝试执行新任务

### ==2. synchronized 锁升级细节==

**==偏向锁什么时候撤锁==**

- 当另一个线程尝试竞争锁时，偏向锁升级为轻量级锁
  - 前提是原持有线程退出同步块

**==轻量级锁如何工作==**

- 线程在自己的栈帧中拷贝锁记录（Lock Record），通过 CAS 尝试将对象头中的 Mark Word 替换为指向自己锁记录的指针
- ==失败则自旋，自旋一定次数后升级为重量级锁==

**锁消除 / 锁粗化是什么？**

- **锁消除**：JIT 编译时发现某些锁不可能被多线程竞争（如局部 StringBuffer），直接删掉锁
  
- **锁粗化**：将相邻的同步块合并，减少加锁次数

### ==3. volatile 与内存屏障==

**volatile 禁止重排的具体实现？**

- 通过内存屏障
  - StoreStore、StoreLoad、LoadLoad、LoadStore

- JVM 在 volatile 写前后插入屏障，保证写操作之前的所有普通写都已完成，且写结果立即对其他线程可见

**volatile 能保证 64 位变量（long / double）的原子性吗？**

- 可以
- JVM 规范要求volatile long / double 的读写必须是原子的
  - 否则可能出现半个值

### ==4. ThreadLocal 内存泄露详解==

**==为什么说 key 是弱引用还会内存泄露？==**

- Entry 继承 `WeakReference<ThreadLocal<?>>`，key 是弱引用，GC 时 key 会变为 null
- ==但 value 是强引用，如果当前线程一直存活（如线程池中的线程），`key == null` 的Entry永远无法被清除，导致value泄露==

**如何彻底解决？**

- 使用 `ThreadLocal.remove()`删除当前线程的变量
- 或使用`static ThreadLocal` + 主动remove

### ==5. AQS 核心==

**AQS 是什么？**

- 抽象队列同步器，ReentrantLock、CountDownLatch、Semaphore 的底层基础

**==核心状态变量==**

- `volatile int state`
  - 0 表示未锁定，> 0  表示被持有

**==获取锁失败怎么排队？==**

- 节点（Node）进入 CLH 队列（FIFO 双向链表），被阻塞（`LockSupport.park`）
  - CLH 队列（Craig，Landin，Hagersten 发明的双向 FIFO 队列）


**独占锁与共享锁的区别？**

- **独占（ReentrantLock）**：state 只能被一个线程加 1
  
- **共享（CountDownLatch）**：state 可以被多个线程同时减少

### ==6. 原子类与 CAS==

**CAS 是什么？有什么问题？**

- Compare And Swap，乐观锁，底层 CPU 指令（`cmpxchg`）
- **==问题==**：ABA（加版本号解决）、自旋开销、只能保证一个共享变量原子性

**AtomicInteger 如何保证原子性？**

- Unsafe 类的 compareAndSwapInt，循环重试

### ==7. Lock 接口与 synchronized 区别==

| 区别       | synchronized           | ReentrantLock                    |
| ---------- | ---------------------- | -------------------------------- |
| 是否可中断 | 不可中断（除非抛异常） | `lockInterruptibly()`可中断      |
| 超时获取   | 不支持                 | `tryLock(timeout)`               |
| 公平锁     | 非公平                 | 可构造公平锁（但性能差）         |
| 条件变量   | wait / notify          | 多个 Condition（await / signal） |

### ==8. 线程状态与通信==

**==线程 6 种状态==**

- NEW
- RUNNABLE
- BLOCKED
- WAITING
- TIMED_WAITING
- TERMINATED

**wait 为什么必须在 synchronized 块中？**

- 需要先持有锁才能释放锁，否则排 IllegalMonitorStateException

**wait 和 await的区别**

- wait 是 Object 方法，配合 synchronized
- await 是 Condition 方法，配合 ReentrantLock

## 补充

### 1. 死锁 4 个必要条件

- 互斥
- 持有并等待
- 不可剥夺
- 循环等待

### 2. `Thread.yield()` 和 `Thread.sleep(0)` 的区别

- yield 只是提示调度器可以让出 CPU，但不一定生效，且不会进入 TIMED_WAITING 状态
- `Thread.sleep(0)` 会触发线程重新竞争 CPU 时间片，但不会释放锁

### 3. CompletableFuture 基本用法

```java
// 异步编排、回调、组合多个异步任务
CompletableFuture.supplyAsync(() -> "hello")
    .thenApply(String::toUpperCase)
    .thenApply(System.out::println);
```



### 4. CopyOnWriteArrayList 原理

- 写时复制，读操作无锁，适合读多写少场景
- **缺点**：内存占用高、数据最终一致性
