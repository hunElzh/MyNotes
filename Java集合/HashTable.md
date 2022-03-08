> HashTable 是一个线程安全的HashMap

### HashTable 的基本参数

- 初始容量：11
- 负载因子：0.75
- 扩容机制：HashTable 当前容量 * 2 + 1（当 HashTable 中元素数量大于 当前容量 * 负载因子 数量时，会进行扩容）
- 桶坐标的计算：key **经过一次 hash** 获取到的哈希值 / 当前 HashTable 容量

### HashMap 和 HashTable 的区别

- HashMap 线程不安全，HashTable 线程安全
- HashMap 的 key / value 都能为null； HashTable key / value 都不能为 null