### ThreadLocal的基本用法

~~~ java
public void set(Object value)：设置当前线程的线程局部变量的值

public Object get()：返回线程所对应的线程局部变量
    
public void remove()：将当前局部变量的值删除，可以减少内存的占用
    
protected Object initialValue()：返回该线程局部变量的初始值
~~~



### ThreadLocal的实现原理

> ThreadLocal类中有一个Map，用于存储每一个线程的变量副本，Map中元素的键为线程对象，值为对应线程变量副本