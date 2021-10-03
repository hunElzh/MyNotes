## java创建一个对象的过程

在刚接触 Java 的时候，我们所熟知的对象创建过程是通过 new 关键字创建的，new这个关键字配合对应类的构造方法，实在是太好用了，底层隐藏了太多细节，一句 Person p = new Person(); 就直接把对象创建出来了，因此完全忽略了 Class 对象的存在。

在 Java 中，对象实例的创建过程大致是：.java 文件中的 Class 类，经过编译之后生成字节码文件 .class，JVM 为 .class 文件创建了一个名为 Class 的对象，JVM 通过 Class 对象中的信息创建出类所对应的实例对象。

下图是创建对象的详细过程，从图片中我们可以得出，在代码层面，只要得到了对应的 **Class对象**，JVM 就会帮我们创建对应类的实例对象，因此对于创建一个实例而言，最关键的一步就是——得到**对应类的Class对象**。

![asfdj3ja;sdj3jgx8829](..\img\asfdj3ja;sdj3jgx8829.png)

## 类加载器（ClassLoader）

> 类加载器的作用就是将 .java 类文件加载成为 .class 字节码文件。本节主要内容为反射，因此本节仅简略介绍类加载器。

- 从loadClass()方法的参数中我们可以看出，我们仅需要将类名作为参数传递给类加载器，他就会帮我们把类加载
- 从loadClass()方法的访问修饰符得出，loadClass()多在被继承时被调用
- 在loadClass()方法中会调用 findLoadedClass() 和 findClass() 两个方法。前者是检查对应类是否已经被加载，后者是加载对应类。

~~~ java
public abstract ClassLoader{
    
    ...
        
	protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException{
        synchronized (getClassLoadingLock(name)) {
            // 首先，检查是否已经加载该类
            Class<?> c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    // 如果尚未加载，则遵循父优先的等级加载机制（所谓双亲委派机制）
                    if (parent != null) {
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }

                if (c == null) {
                    // 模板方法模式：如果还是没有加载成功，调用findClass()
                    long t1 = System.nanoTime();
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }

	// 子类应该重写该方法
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        throw new ClassNotFoundException(name);
    }
    
    ...
    
}
~~~



> 额外知识点，ClassLoader是抽象类（其中包含抽象方法的类被称为抽象类），而抽象类是无法直接通过new来创建对象，抽象类可以通过abstract父类的引用指向子类实例（多态的向下转型）
>
> abstract A，class B；B extends A；A a = new B()

​		

- ClassLoader 中的 findClass()方法体仅抛了一个异常，所以根本就无法去加载.class文件，因此想要加载一个类的正确做法是子类重写覆盖findClass()方法，具体代码如下
- ClassLoader 中的 defineClass(name, datas, 0, datas.length) 方法，目的是根据 .class 文件的字节数组 byte[] b 造出一个对应的 Class 对象。defineClass最终会调用一个 native 方法，因此具体原理暂不明晰。

~~~ java
public TestLoad extend ClassLoader{
    @Override
    public Class<?> findClass(String name) throws ClassNotFoundException {
        try {
            /*自己另外写一个getClassData()
                      通过IO流从指定位置读取xxx.class文件得到字节数组*/
            byte[] datas = getClassData(name);
            if(datas == null) {
                throw new ClassNotFoundException("类没有找到：" + name);
            }
            //调用类加载器本身的defineClass()方法，由字节码得到Class对象
            return defineClass(name, datas, 0, datas.length);
        } catch (IOException e) {
            e.printStackTrace();
            throw new ClassNotFoundException("类找不到：" + name);
        }
    }	
}
~~~



​		类的字节码加载过程大致如下

![v2-5f8d9252d2d8d65573b342a344fc5697_720a](..\img\v2-5f8d9252d2d8d65573b342a344fc5697_720a.jpg)



## java.lang.Class类（Class）

> 当类被 ClassLoader 加载至内存之后，由于 java 语言无法直接操作内存，所以如果我们想利用类中的数据，那我们就得将其存储保存在一个类中，因此 java 设计者就创造了 Class 类，用于存储并处理 .class 字节码文件中加载之内存中的数据

### Class类的构造方法

​		由下图我们可以看出 Class 文件的特殊性，设计者用 final 关键字定义类，使其无法被继承；随后用 private 关键字修饰其构造方法，使得 Class 类无法通过 new 关键字进行实例化，因此 Class 对象只能由 JVM 创建。

​		在JVM构造Class对象的时，需要传入一个类加载器，随后才有之前的一系列加载、创建的过程。

![B9D5QNGULVREDKJ-DGIA7B](..\img\B9D5QNGULVREDKJ-DGIA7B.png)



### Class类的方法

- Class.forName( 类路径 ) 方法：作用是将对应类文件 解码成为 字节码文件，其本质还是调用了loadclass()方法。在调用forName() 方法之后，对应 java类会被加载进内存，随后内存中的数据被Class类的字段接收存储。

  ![IVGGB~XNT6Q0YLOU8IE](..\img\IVGGB~XNT6Q0YLOU8IE.png)

- clazz.newInstance()方法：本质是调用无参构造类(Constructor)的 newInstance()方法，最终达到创建对应类的实例的目的，调用这一方法之后，所指定的对象就已经被实例化了。



### Class类的字段

​		下面是一个涵盖内容较为全面的类，其中包含的信息有，这些信息在字节码文件中都会以01代码的形式展现，供机器阅读。BaseDto 类中包含信息如下

- 权限修饰符（public）
- 类名（BaseDto）
- 参数化类型/泛型信息（T）
- 接口（implement）
- **字段**（private String id）
- **构造方法**（BaseDto()、BaseDto(String id)）
- **方法**（getId()、setId()）

~~~ java
public final class BaseDto<T> implements Serializable {

    @NotNull
    private String id;

    private String name;

    private BaseDto() {

    }

    public BaseDto(String id) {
        this.id = id;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    private void setName(String name) {
        this.name = name;
    }
}
~~~



​		

​		在 Class 类中有一个子类 ReflectionData，它其中定义了一些数组，用于指向字节码文件存储在内存中的字段、构造方法、方法，并且Class类中还定义有专门的字段用于指向内存对应类的中的注解信息、泛型类型等。

![HGCI8DEC21P](..\img\HGCI8DEC21P.png)

![v2-40ce6b148b43a464df1a893fa5dc6978_720w](..\img\v2-40ce6b148b43a464df1a893fa5dc6978_720w.png)

![v2-da2f79b3bb318554d16b5101e1f59427_720w](..\img\v2-da2f79b3bb318554d16b5101e1f59427_720w.png)

- 注解数据和泛型信息属于 Class 自定义类

- 字段、构造方法、方法这仨由于信息量过大，JDK 单独设计了三个类：Field、Method、Constructor，用于封装与其相关的数据

  

  下图是 Method 类中相关信息，Field、Contructor类似

![v2-d31b7dedef1bd4eb470305751b287bb7_720w](..\img\v2-d31b7dedef1bd4eb470305751b287bb7_720w.jpg)

​		



### 创建Class类的三种方法

其实归根结底，我们还是向得到对应类的Class类，下面是获取Class类的三种方法

1. Class.forName(xxx): Class<Person> clazz = Class.forName("com.lzh.person")
2. xxx.class: Class<Person> clazz = Person.class;
3. xxx.getClass(): Class<Person> clazz = Person.getClass();



### 通过类的Class类创建对象

上面我们已经通过Class类获取到了类的字段、方法、构造器...，虽然拿到了那么多东西，但是我们却仍旧不知道如何实例化一个对象。

我们传统创建对象实例的方式是 new BaesDto() 或者 new BaseDto(参数) ，这两者的区别就在于一个调用的是类中无参的构造方法，另一个调用的是类中带参的构造方法。嗯？为什么要调用构造方法？不知道发生了什么那就只好去稍微探究一下 Constructor 源码了。

在 Constructor类 的源码中有一个名为 newInstance(Object ... initargs) 的方法，方法名也很直观——新建实例，下面是 JDK 作者对它的描述，大概意思就是，当构造器调用了这个吊方法之后就会创建出一个实例，嗯，确实很酷。

![4SPD77CGYKWJ9GTR2](E:\Java\GitRepository\MyNotes\img\4SPD77CGYKWJ9GTR2.png)



### 一个栗子

​		BaseDto是我们上面定义的一个类，此类被final修饰因此无法被继承，并且其构造方法被private修饰，因此无法直接通过 new 对其进行直接实例化。所以想要使用该类就只能通过反射的方式，调用反射API才能使用其之中的方法、访问其之中的字段

![3KHCC7UUTQVLV](..\img\3KHCC7UUTQVLV.png)





































