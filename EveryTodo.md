## 2022/2/15

### 并发之JUC相关知识

#### JUC主要包括

- 原子类（AtomicXXX）
- 锁类（XXXLock）
- 线程同步类（AQS、CountDownLatch）
- 任务执行器类（Executor体系类）
- 并发集合类（ConcurrentXXX）
- 阻塞队列类（BlockingQueue）
- Future相关类
- 其他一些辅助工具类



#### AQS 

> AQS 全称 AbstractQueuedSynchronizer 译为 抽象队列同步器

在 AQS  中维护了

- 一个 state 变量：代表竞争资源的标识，当 state 变量不为 0 时，线程阻塞
- 一个 队列：存储竞争资源失败的线程的队列

我们常见的 CountDownLatch、CyclicBarrier、Semaphore、ReentrantLock 都实现了 AQS 

AQS 采用抽象模板的方式，将其实现类分成

- 独占模式：代表有 ReentrantLock，ReentrantLock.lock() 方法会使 state + 1，ReentrantLock.unlock() 方法会使 state - 1
- 共享模式：代表有 CountDownLatch，初始设置的参数既是 state 值



#### CountDownLatch & CyclicBarrier

共同点

- 都实现了 AQS，并且都为 AQS 的共享模板
- 都是通过内置一个计数器来控制共享访问

不同点

- 线程调用 CountDownLatch.await 暂停之后，在其他线程中调用 `CountDownLatch.countdown()` 方法，都会使内置的 AQS 的 state - 1， 当它其中维护的变量变为0之后，调用 CountDownLatch.await() 方法的线程会被唤醒
- 当线程每次调用 `CyclicBarrier.await()` 方法时，都会使内置 AQS 的 state - 1， 当它其中维护的变量变为0之后，调用 CyclicBarrier.await() 方法的线程就会继续运行



#### 为什么 wait() 和 notify() 不在 Thread 中？

总所周知 Java 中的锁是对象级别的。wait()、notify()、notifyAll() 的作用：wait() 的作用是让当前对象中的持锁线程**释放对象锁**并进入对象的WaitSet中；notify()是随机唤醒对象WaitSet中的一个线程，让他持有锁并执行锁中代码；notifyAll() 是唤醒对象 WaitSet 中的所有线程并让他们争抢锁。他们一个个都是操作锁对象中的底层的数据结构。

因为 Java 中的锁是对象级的，Java 中的每个对象都可以被看作成为一个**监视器（monitor）**，并且每个对象都由三部分组成：

- 进入区(Entry Set)：一个新晋线程进入对锁的争抢，如果抢到了就执行线程，否则进入等待区
- 拥有者(Owner)：持锁的正在执行的线程
- 等待区(Wait Set)：调用 wait() 方法后，释放锁，进入此区域，此时区域中的线程都等待被唤醒



#### final、static、final static修饰的变量分别存放在哪？

- final 修饰的变量：存放在方法区 中的 运行时常量池中
- static 修饰的变量：存放在方法区中的静态区中，并且跟随着类的加载而加载



#### 为什么 Java 不能多继承

Java 是一项面向对象的编程语言，其有一个叫做多态的特性，当使用多态创建一个对象实例之后，该对象实例可以访问父类的字段和方法，此时如果有多继承，并且父类有同名方法的话，程序在执行过程中的方法调用就会产生错乱