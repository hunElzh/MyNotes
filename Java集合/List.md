## ArrayList



### ArrayList的扩容机制

ArrayList的底层是动态数组，决定ArrayList容量大小的方式有以下四种

1. ArrayList()：ArrayList长度等于0
2. ArrayList(int initialCapacity)：会使用指定容量的数组
3. add(Object o)方法扩容：首次扩容为10，再次扩容为上次扩容的1.5倍（ x>>1 + x ）
4. addAll(Collection c)方法扩容：当c中没元素时，默认扩容长度为10；当c中有元素时，list的容量在下次扩容的容量和实际元素的个数中选择一个较大值
   - 例子，先往一个list中塞10个元素，此时list长度刚好为10，现在对list执行addAll()方法。addAll(3)加入一个长度为3的集合后，此时list长度为15（小于10的下一次扩容）。add(6)加入一个长度为6的集合后，此时list的长度为16（大于10的下一次扩容15）

**ArrayList每次扩容都是复制原数组并在其基础上进行扩容，随后改变list实例的指向，此时原数组没有被引用，因此被JVM回收**



### fail-fast & fail-safe

#### fail-fast（ArrayList)

> 一旦发现遍历的同时其他人来修改，立刻抛异常

实现原理：在遍历开始时，Itrator 会使用 exceptedModified 来接受一个 list 传来的参数 modified。modified 参数记录list 被改变的次数，当在遍历 list 集合过程中，对list进行修改操作，modified的值就会发生改变。循环结束后，Iterator的exceptedModified与list的modified不相等，就会抛出ConcurrentModificationException()异常

#### fail-safe（CopyOnWriteArrayList)

> 发现遍历的同时其他人来修改，应当有应对策略，例如牺牲一致性来让整个遍历运行完整。即此次遍历无法遍历出遍历过程中的新增的元素

实现原理：CopyOnWriteArrayList的 list 的数组与迭代器是分离的（不太好说），每次调用add()方法会复制原数组并使数组长度+1（区别于ArrayList的扩容机制）





## LinkedList

### ArrayList和LinkedList的区别

- ArrayList
  1. 基于数组，需要连续内存
  2. **随机访问**快
     - list中存储的对象大小都一致例如int为4字节，又由于内存连续，因此只要知道list的头和元素所在索引，经过简单计算即可查询到元素
     - 要区别随机访问与查询，查询指的是根据元素存储的内容获取到对应索引，ArrayList 和 LinkedList 的查询效率都是O(n)，查询效率高的HashMap等
  3. 尾部插入、删除性能可以，其他部分插入、删除都会移动数据导致性能降低
  4. 可以充分利用 CPU 缓存、局部性原理
     - CPU 缓存：若CPU直接读取内存中的数据，将会很耗时，因此引入了CPU缓存，CPU缓存即使对内存的缓存（CPU还有一级缓存、二级缓存、三级缓存，套娃了属于是）
- LinkedList
  1. 基于双向链表，无需连续内存
  2. **随机访问**慢（需要遍历链表）
  3. 头部插入、删除性能高（若在中间插入、删除，还得先遍历定位才可进行增删操作。实际情况中间插入LinkedList要比ArrayList性能低很多）
  4. 占用内存多









 