## static

tatic用于修饰成员变量和成员函数，**想要实现对象中的共性数据的对象共享**，可以将这个数据进行静态修饰，被静态修饰的成员可以直接被类名调用，静态随着类的加载而加载，而且优先于对象存在。

静态方法只能访问静态成员（静态方法和静态变量），不可以访问非静态成员，这是因为静态方法加载时，优先于对象存在，所以没有办法访问对象中的成员。静态方法中不能使用this和super关键字，因为this代表本类对象，super代表父类对象，而静态时，有可能没有对象存在，所以this和super无法使用。

## java中的this与static

可以理解为this是java对象内部持有的一个引用，当调用某个方法（字段）时，将方法（字段）传递至这个对象引用，此时就能知道对象是在操作哪个方法（字段）。



### java中的static方法与static字段

> static修饰的字段或方法都是**属于一个类**的，在这个类中是被共享的。

 - static字段：是大家共有的，不是特定的异化数据（人的身高体重是异化数据，不能用static修饰；国家的总人口就不是特定的异化数据）

 - static方法：当一个方法只提供通用的操作流程，而不会在其内部引用对象字段（数据）时【todo不太理解】，此时我们可以把这个方法定义成为一个静态方法。（即独立出来的处理数据的一个方法）

   

   **static方法 无法访问实例字段、实例方法，只能访问静态数据、静态方法**

   1. 解释1：对象中的方法在调用已定义的方法、字段时，会隐式地在其前添加一个this，而静态方法中不存在this，因此无法调用实例方法、实例字段

   2. 解释2：静态方法是随着类加载而加载的，因此在静态方法加载完成时，实例字段、实例方法还没有被实例化，此时静态方法自然就访问不到实例字段、实例方法，因此编译时就会报错。



它也是大家共有的，因此static方法在设计上是不能处理差异化数据，因此他不能直接访问static字段。


<<<<<<< HEAD:Java基础/static与this.md



## Java初始化顺序

1. 初始化父类的静态方法
2. 初始化父类的成员变量
3. 初始化父类的构造方法
4. 初始化子类的静态方法
5. 初始化子类的成员变量
6. 初始化子类的构造方法
=======
~~~ java

~~~
>>>>>>> 18ec54baa0adc3e4e1e845e11db9cd4349d7eb8b:Java基础/理解static.md

