### ConcurrentHashMap 基本介绍

> Segment 数组保证 1.8 之前 ConcurrentHashMap 线程安全

1. 1.8 之前 ConcurrentHashMap 使用 Segment + 数组 + 链表的结构，**每个 Segment 对应一把锁**，如果多个线程访问不同的 Segment，则不会发生冲突
2. 1.8 开始 ConcurrentHashMap **将链表的每个头结点作为锁**，底层数组结构为 数组 + 链表/红黑树
3. 1.8 之前 ConcurrentHashMap  初始化为懒汉式初始化 1.8 之后 ConcurrentHashMap 初始化为饿汉式初始化



### ConcurrentHashMap 1.8之前

#### 参数介绍

- capacity：Segment 数组数量
- factor：负载因子
- clevel：并发度（初始化时固定）

#### 重要数据计算

- HashEntry 下标计算：hash(hash(key)) 的高 n 位的值（n 的计算方式：2^n=clevel）
- HashEntry 数组个数 = capacity / clevel（HashEntry 数组最小值为 2）
- key 桶下标计算：hash(hash(key)) 的最 n 位（n 的计算方式：2^n = HashEntry 数组个数）

#### 扩容机制

HashEntry * factor > 当前在 HashEntry 中存储的元素个数时发生扩容，HashEntry数组数量翻倍