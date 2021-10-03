## 模板代码与泛型（此章说法有误）泛型这一章节的细节还需打磨

在JDK1.5以前，ArrayList存储数据餐区的方式是：在内部塞入一个Object[] array

~~~ JAVA
public class ArrayList {
    private Object[] array;
    private int size;
    public void add(Object e) {...}
    public void remove(int index) {...}
    public Object get(int index) {...}
}
~~~

在JDK1.5以前的ArrayList存储String类型会有这么两个缺点

1. 需要强制转型
2. 不方便，易出错

此时我们想要使用ArrayList存储并获取String数据类型的数据需要则么写

~~~ java
ArrayList list = new ArrayList();
list.add("Hello");
// 获取到Object，必须强制转型为String:
String first = (String) list.get(0);
~~~

为什么在获取存入的数据时需要转型？因为在存储之前真正的数据类型是String，而存入后String被向上转型为Object，随后想要再通过String拿到之前存储的数据，就得再进行一次向下转型 (String)Object，那我们为啥不直接用Object类型呢？为什么要再向下转型成String数据类型呢？傻瓜，不转型的话，能用到子类中的那么多好用的方法吗？String中的那么多方法，它不香吗？

作为一种解决方法，JDK1.5之前程序员会为`String类型`单独编写一种`ArrayList`

~~~ java
public class StringArrayList {
    // 因为这种ArrayList只存String，所以不需要用Object[]兼容所有类型，只要String[]即可
    private String[] array;
    private int size;
    public void add(String e) {...}
    public void remove(int index) {...}
    public String get(int index) {...}
}
~~~

那与上述同理，如果要存储Integer、User...等等各种各样的特定类型，那哪有那么多精力？为了解决这一问题，JDK设计者就设计出来了一个模板，这个模板可以接收任意的引用数据类型，我们将这个模板称作——泛型。

~~~ java
public class ArrayList<T> {
    private T[] array;
    private int size;
    public void add(T e) {...}
    public void remove(int index) {...}
    public T get(int index) {...}
}
~~~

上面这个T，可以接收任何class类型，看下面代码

~~~ java
// 创建可以存储String的ArrayList:
ArrayList<String> strList = new ArrayList<>();
// 创建可以存储Float的ArrayList:
ArrayList<Float> floatList = new ArrayList<>();
// 创建可以存储Person的ArrayList:
ArrayList<Person> personList = new ArrayList<>();
~~~

卧槽，突然神奇了起来，这个T它凭什么可以接收仍和引用类型的代码？

站在巨人的肩膀上，我们已经知道了答案。我们知道，程序源码要进行一次编译，而泛型的模板类型T，正式在编译时，接收到传递而来的数据类型，此时它就自动被编译成为对应的引用类型，它底层还是一个Object，只不过编译器会在编译时将Object转变为String

~~~ java
// 嘿嘿，我想把ArrayList<T>填充为ArrayList<String>，专门收纳String类型
ArrayList<String> strList = new ArrayList<>();

// 在编译之前还是Object接收数据的，也正因如此编译器不会警告、报错
public class ArrayList {
    private Object[] array;
    private int size;
    public void add(String e) {...}
    public void remove(int index) {...}
    public String get(int index) {...}
}

//当接收String之后，上面的ArrayList就被转换成为了String类型接收数据了，也正应如此被它在后续进入内存时也是String类型
public class StringArrayList {
    private String[] array;
    private int size;
    public void add(String e) {...}
    public void remove(int index) {...}
    public String get(int index) {...}
}
~~~

编译器将源码编译成字节码时，会识别源码中的数据类型，使得编译后的程序会变化，这就是泛型时间的底层逻辑。







## 形式类型参数、实际类型参数

> 我们需要弄清楚一个概念，泛型是一个技术，他将各种引用的数据类型抽象成一个模板类型T，而这个T不能被称作泛型，他是被泛型抽象出来的一个模板类型的代称
>
> 其实模板类型T的底层还是Object

- 形式类型参数（type parameter 又称类型参数）：ArrayList<T> 中的T不叫泛型，而叫类型参数。在之前的习惯中，我们通常将这个T称作为泛型，但这其实是不对的，泛型本质上是一种技术，他将变量类型抽抽取成类型参数，而这个T就是抽取出来的模板类型。
- 实际类型参数（actual type parameter）：使用泛型时，ArrayList<String>中，T被替换为String，可以看作是对T的”赋值“，而这其中的String就是实际类型参数。

我们还可以把泛型理解为：变量是对数据的抽取，而泛型时对变量的抽取。泛型将变量抽取成类型参数，抽象层次更高。



#### 下面用伪代码举一个将数据抽取成变量，再将变量抽取成类型参数的例子

1. 最原始状态，变量都还未被抽取

~~~ java
public User getUserBy1() {
    return userDao.get(1);
}
public User getUserBy1() {
    return userDao.get(1);
}
~~~

2. 将变量抽取出来：对上面1，2这两个具体**数据进行抽取**，这也是我们常做的。

~~~ java
public User getUser(Integer id) {
    return userDao.get(id);
}
~~~

3. 从例子2中我们可以看出来，返回类型是一个封装过的User，如果具体场景想让我们获取到User的姓名、电话、学校...。这时我们可能就要编写很多的getUser()来返回对应类型的数据。

~~~ java
public User getUser(String name) {...}
public User getUser(Date birthday) {...}
~~~

4. 遇到3这种情况，我们改如何去做呢？没错！我们这就要用到我们上述所说的**泛型**！通过泛型将数据类型抽取出来，这样我们就可以处理传入什么类型数据就返回什么类型数据的业务逻辑了！

~~~ java
public User getUser(T t){
    return UserDao.get(t);
}
~~~

最后强调一点，泛型只能对引用数据类型进行抽取，而不能对基本数据类型抽取。啊？那为什么我们使用 模板类型T 的时候，它能接收整形、浮点型...各种基本数据类型的数据啊？我们整形、浮点型其实传递的都是其封装好的引用数据类型！比如 Integer、Double......































