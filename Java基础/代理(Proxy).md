## 代理设计模式概括

> 代理是一种设计模式，提供了对目标对象的简介访问方式，即类通过代理访问目标对象。如此便于在目标对象方法实现的基础上增加额外的功能操作——前拦截，后拦截等，以满足自身业务需求。

![v2-6e2fa8c8c02e0f04a601cdd951045f82_720w](..\img\v2-6e2fa8c8c02e0f04a601cdd951045f82_720w.png)

常用的代理方式可以粗略划分为：静态代理和动态代理。

静态代理的基本实现：编写一个代理类，实现与目标对象相同的接口，并在内部维护一个目标对象的引用。通过构造方法将目标对象注入进代理对象中，此时目标对象就成为代理类中的一个字段（属性），此时可以通过代理对象中调用目标对象的同名方法，并添加前拦截、后拦截等所需业务功能。



## 不使用代理，直接修改源程序的例子

> 在没有应用代理的设计模式之前，我们访问对象的方法如下图所示

![0C~BG9$EEHTSVT2X7KR6KJ](..\img\0C~BG9$EEHTSVT2X7KR6KJ.png)

现在有一个计算器类，想在其运算开始和结束时打印日志

~~~ java
public class Calculator {

    public int add(int a, int b) {
        int result = a + b;
        return result;
    }

    public int substract(int a, int b) {
        int result = a - b;
        return result;
    }

}
~~~

最简单的方法就是修改Calculator类

~~~ java
public class Calculator {

    public int add(int a, int b) {
        System.out.println("add start...");
        int result = a + b;
        System.out.println("add end...");
        return result;
    }

    public int sub(int a, int b) {
        System.out.println("sub start...");
        int result = a - b;
        System.out.println("sub end...");
        return result;
    }

}
~~~

直接修改Calculator类存在的问题

1. 直接修改源程序，不符合开闭原则。应对扩展开发，对修改关闭
2. 如果类似 Calculator 的类有几十、上百个的话，这样修改量太大
3. 存在很多重复代码（日志打印代码写在所有核心代码前后，若核心代码很多，就会显得十分冗杂）
4. 日志打印硬编码在源程序中，不利于后期维护：若加入Caculator类日志需求更改成为打印算式详细过程，那这样需要到源程序中一行行更改，不仅麻烦，还有可能一次性无法完全更改的情况出现！

因此直接修改源程序不可取！



## 使用静态代理实现上面的 Caculator 类

- 将之前的 Caculator 类改为接口
- 创建目标类 CaculatorImpl 实现 Calculator
- 创建代理类 CaculatorPorxy 实现 Calculator

~~~ JAVA
//创建一个Calculator接口
public interface Calculator {
    public int add(int a, int b);
    public int sub(int a, int b);
}
~~~

~~~ JAVA
//创建一个Calculator类实现接口中的方法
public class CalculatorImpl implements Calculator {

    @Override
    public int add(int a, int b) {
        int result = a + b;
        return result;
    }

    @Override
    public int sub(int a, int b) {
        int result = a - b;
        return result;
    }
}
~~~

~~~ JAVA
//创建一个Caculator代理对象，在其中引入目标对象，并重写方法，此后调用代理对象，并将目标对象作为参数传递给代理对象，就能实现对目标对象的静态代理
public class CalculatorProxy implements Calculator{

    //将带泪对象内部维护一个目标对象的引用
    private Calculator target;

    public CalculatorProxy(Calculator target) {
        this.target = target;
    }

    //相当于在代理方法中再次调用了目标对象的方法，但是代理对象在没有修改目标对象的前提下对其进行了扩展
    @Override
    public int add(int a, int b) {
        System.out.println("add方法开始...");
        int result = target.add(a, b);
        System.out.println("add方法结束...");
        return result;
    }

    @Override
    public int sub(int a, int b) {
        System.out.println("subtract方法开始...");
        int result = target.sub(a, b);
        System.out.println("subtract方法结束...");
        return result;
    }
}
~~~

静态代理保证了设计原则中的开闭原则，即对目标对象扩展开放，修改关闭，但是也仅解决了这一个缺点，还剩下三个缺点

1. 如果Caculator有几十个、上百个方法，修改量太大
2. 存在重复代码（使用静态代理，日志打印代码重复写在多个代理类中类中）

3. 日志打印硬编码在代理类中，不利于后期维护：若加入Caculator类日志需求更改成为打印算式详细过程，那这样需要到源程序中一行行更改，不仅麻烦，还有可能一次性无法完全更改的情况出现！

### 静态代理存在的问题

上面这个案例中，代理类是我们事先编写的，而且要和目标对象实现相同接口。由于 CalculatorImpl（目标对象）需要日志功能，于是乎就编写了 CalculatorProxy（代理对象），并通过代理对象的构造器传入目标对象，调用目标对象同名方法的同时，添加增强代码。

此案例中有一个验证共你的问题，代理对象构造器接收的参数类型是 Calculator，这意味着它只能接收Calculator的实现类对象，亦即我们写的代理类 CalculatorProxy 只能给 Calculator 做代理，这个代理对象与 Calculator 绑定死了。假设此时我们的系统需要全面改造，给其他类也添加日志打印功能，就得为其他几百个接口都写一份代理类，海量的工作量显然不是我们想要达到的最终目的。

每对目标对象进行扩展，我们就需要手动编写一个代理类，通过代理类实例化出来的代理对象，我们即可实现对当前目标对象的扩展，由此可见，**我们想要的其实并不是这个存储在硬盘上的代理类，而是代理类在运行过程中实例化出来存储在内存中的代理对象**。那么能否让 JVM 根据接口自动生成代理对象呢？又或者说，又没有这样一种方法，当我们传入一个接口时，它可以给我们返回一个接口的代理对象。

![v2-04157e707582d34233d6ae0eb5bf6867_720w](..\img\v2-04157e707582d34233d6ae0eb5bf6867_720w.png)





## 动态代理

> 结论：静态代理通过编写**代理类实现了增强代码与目标对象的解耦**，但此时代理类却与增强代码耦合了，而动态代理就是JVM根据**目标对象在内存中动态生成代理对象并给代理对象附上增强代码**，最终达到代理类与增强代码解耦的目的。

### 动态代理的目的是要实现

1. 自动生成代理对象，免去编写代理类的痛苦
2. 增强方法与代理类解耦，达到增强方法的复用

根据结果推原因，我们最终想要实现的是，拿到 **目标对象** 和 **增强代码**，而代理对象则可以自动生成。

### 简单温习对象的生成过程

1. ClassLoader 将 .class 文件加载至内存
2. 执行静态代码块和静态初始化语句
3. 构造器调用 newInstance() / newInstance(param)，创建一个空的对象
4. 子类调用父类构造器
5. 执行构造器（执行构造代码块和初始化语句）

由上述对象实例化过程我们可以得知，想要实例化一个对象，只需要得到该对象的 Class类 就足够了，对象的 Class类 有如下三种创建方式。`Class类是泛型类，它可以根据传入的泛型改变对应类型`

1. Class clazz = class.forName("com.lzh.Person");
2. Class clazz = Person.class;
3. Class clazz = Person.getClass();

这三种创建Class对象的方法，无不需要对应的类，此时我们不得不从其他方面入手（接口和父类），以获取到对应的代理对象。



### 调用代理对象与调用目标对象方法的区别

先看下面这个例子

~~~ java
public Interface Calculator{
    int add(int a, int b);
}

//目标对象
public Class CalculatorImpl implements Calculator{
    public int add(int a, int b) {
        return a + b;
    }
}

//代理对象
public Class CalculatorProxy{
    CalculatorImpl target;
    
    public Proxy(Calculator target) {
        this.target = target;
    }
    
    public int add(int a, int b) {
        ----------log before---------
        target.add(a, b);
        ----------log after----------
    }
}
~~~

细品这个例子，外部类调用 Calculator 的 add() 方法其实和调用 实现类和代理类的 add() 方法没啥区别，因此代理类或者说代理对象它重要吗？他其实只是一个空壳，它仅仅只是调用了目标对象的方法，并在目标对象方法周围添加了一段增强代码而已。代理类？呵，空壳而已，不写在硬盘中一点儿影响也没有！

**因此可以得出动态代理的最终目的：为 目标对象 增加 增强代码 。**



## JDK动态代理

> 书接上文，我们现在需要得到的东西是 目标对象 和 增强代码。但此时，我们还仅初步知道，代理对象按道理来说是可以通过反射生成的，但是具体生成过程如何呢？接下来慢慢推断。

### 观察 接口 与 实例类 的区别

> 结论：在JDK动态代理过程中，所需要代理的目标对象的信息，可以通过接口获得

首先通过下面程序输出结果，观察一下接口和一个实例类的Class对象的区别

~~~ java
public class InterfaceClassDiff {

    public static void main(String[] args) {

        //得到Calculator接口的Calss对象
        Class<Caluculator> calculatorClazz = Caluculator.class;
            
        //获取对象的 构造器 和 方法
        Constructor<?>[] calculatorClazzConstructors = calculatorClazz.getConstructors();
        Method[] calculatorClazzMethods = calculatorClazz.getMethods();
        
        System.out.println("------接口Class的构造器信息------");
        printClassInfo(calculatorClazzConstructors);
        System.out.println("\n");
        System.out.println("------接口Class的方法信息-------");
        printClassInfo(calculatorClazzMethods);
        System.out.println("\n");

        //得到CalculatorImpl实现类的Calss对象
        Class<CalculatorImpl> calculatorImplClazz = CalculatorImpl.class;
        
        //获取对象的 构造器 和 方法
        Constructor<?>[] calculatorImplClazzConstructors = calculatorImplClazz.getConstructors();
        Method[] calculatorImplClazzMethods = calculatorImplClazz.getMethods();
        
        System.out.println("------接口Class的构造器信息------");
        printClassInfo(calculatorImplClazzConstructors);
        System.out.println("\n");
        System.out.println("------接口Class的方法信息------");
        printClassInfo(calculatorImplClazzMethods);
        System.out.println("\n");
    }

    public static void printClassInfo(Executable[] targets) {
        for (Executable target : targets) {
            String name = target.getName();
            StringBuilder stringBuilder = new StringBuilder(name);
            stringBuilder.append('(');
            Class<?>[] clazzParams = target.getParameterTypes();
            for (Class<?> clazzParam : clazzParams) {
                stringBuilder.append(clazzParam.getName()).append(',');
            }
            if (clazzParams.length != 0) {
                stringBuilder.deleteCharAt(stringBuilder.length() - 1);
            }
            stringBuilder.append(')');
            System.out.println(stringBuilder.toString());
        }
    }
}
~~~

输出结果如图所示

![1632973481021](..\img\1632973481021.png)

观察 接口的Class类 和 实例类的Class类 的运行结果可以很明确地观察出 接口 和 实例类 的区别

1. 接口的Class没有构造器，所以 Calculator 不能直接 new 对象
2. 实现类的Class有构造方法，所以 CalculatorImpl 可以直接 new 对象
3. 接口Class对象中只有 add() 方法
4. 实现类Class对象中除了有实现的 add() 方法，还有继承自 Object类 中的方法，还能有其自定义的方法（虽然再上图中没体现）

至此，我们至少知道从接口获取 目标对象 的方法的信息是可行的，接下来就是要知道，如何根据一个接口得到代理对象！



### 通过接口，获取代理对象

> 结论：通过Proxy.getProxyClass(ClassLoader, Class) 方法，将接口Class类作为参数传入，即可动态生成代理对象实例。

![32sd092349afjtd](..\img\32sd092349afjtd.png)



通过刚才的例子，我们可以得知：

1. 接口确实包含我们所需要的目标对象的方法信息（CalculatorImpl这个目标对象的add()方法）
2. 接口为什么不能直接 new 对象（接口缺少构造器）

既然我们可以通过接口获取到目标对象的信息，那么是否存在一种机制，能不改变接口本身，直接拷贝接口的信息到另一个Class类中，然后给那个Class类装上构造器呢？嘿，还真有，就是下面这张图。

![1632983331348](..\img\1632983331348.png)

**Proxy.getProxyClass(ClassLoader loader, Calss<?>... interfaces)：返回代理类的Class对象。**



咱们通过以下例子的输出，来观察代理类获取到的信息

~~~ java
public class getProxyClass {

    public static void main(String[] args) {
        /**
         * 参数1：Calculator的类加载器（将目标类实现的接口加载进内存）
         * 参数2：代理对象需要和目标对象实现相同接口（即Calculator类）
         * 最终实现效果：根据目标对象实现的接口生成一个针对目标对象的代理类
         */
        Class<?> calculatorProxyClazz = Proxy.getProxyClass(Calculator.class.getClassLoader(), Calculator.class);
        //比较目标类名和代理类类名
        System.out.println(CalculatorImpl.class.getName());
        System.out.println(calculatorProxyClazz.getName());

        Constructor<?>[] constructors = calculatorProxyClazz.getConstructors();
        System.out.println("-----------代理类的构造器------------");
        printClassInfo(constructors);
        System.out.println("\n");
        Method[] methods = calculatorProxyClazz.getMethods();
        System.out.println("-----------代理类方法-------------");
        printClassInfo(methods);
        System.out.println("\n");
    }


    public static void printClassInfo(Executable[] targets) {
        for (Executable target : targets) {
            // 构造器/方法名称
            String name = target.getName();
            StringBuilder sBuilder = new StringBuilder(name);
            // 拼接左括号
            sBuilder.append('(');
            Class<?>[] clazzParams = target.getParameterTypes();
            // 拼接参数
            for (Class<?> clazzParam : clazzParams) {
                sBuilder.append(clazzParam.getName()).append(',');
            }
            //删除最后一个参数的逗号
            if (clazzParams.length != 0) {
                sBuilder.deleteCharAt(sBuilder.length() - 1);
            }
            //拼接右括号
            sBuilder.append(')');
            //打印 构造器/方法
            System.out.println(sBuilder.toString());
        }
    }
}
~~~

![1632992820508](..\img\1632992820508.png)

上图我们可以得到这些信息

1. 通过 Proxy.getProxyClass() 返回的 Calculator接口 的 代理Class，它是有构造器的！（而 Calculator接口的 Class对象 中是没有构造器的！）
2. 通过 Proxy.getProxyClass() 返回的 Calculator接口 的 代理Class，他拥有目标对象中的所有方法。



上面打印的代理类方法，其中只有 add(int,int) 是自定义的方法其他方法都是 Proxy.getProxyClass() 为我们动态生成的，而这个 add() 方法是由 Proxy.getProxyClass(classLoader, Class) 传入的参数决定的，也正因是由于接口，所以才能准确地定位到目标对象。

**Proxy.getProxyClass(classLoader, Class) 方法仅接收接口的类加载器和Class类作为参数！**



### 通过代理Class插入增强代码！

> 结论：增强代码编写在 InvocationHandler.invoke() 方法之中，并且在 invoke() 方法之中指定增强的目标对象.

我们通过分析下面这段代码来分析如何获取增强代码，并且观察增强代码是如何作用在目标对象之上的。

~~~ java
public class GetProxyInstance {

    public static void main(String[] args) throws Exception {

        //创建Calculator接口的代理对象
        Class<?> calculatorProxyClazz = Proxy.getProxyClass(Calculator.class.getClassLoader(), Calculator.class);

        //获取到唯一有参的构造器 $Proxy(InvocationHandler h)
        Constructor<?> constructor = calculatorProxyClazz.getConstructor(InvocationHandler.class);

        //用构造器执行构造方法，得到 代理对象。此时InvocationHandler作为参数传入
        Calculator calculatorImpl = (Calculator) constructor.newInstance(new InvocationHandler() {
            @Override
            /**
             * Object proxy：代理对象本身（不要调用，否则会进入无限递归）
             * Method method：用来执行方法的一个参数，通过method.invoke(目标对象, 方法参数)，使指定的目标对象每次调用方法时调用的都是增强方法
             * Object[] args：方法参数。
             */
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                // 最low的方法，手动new一个目标对象，此时args可以获取目标对象中所有方法的参数
                CalculatorImpl calculatorImpl = new CalculatorImpl();
                //方法执行前打印日志
                System.out.println(method.getName() + " start");
                //调用method.invoke()方法，传入的参数是 目标对象, 方法参数
                Object result = method.invoke(calculatorImpl, args);
                //方法执行后打印日志
                System.out.println(method.getName() + " end");
                //返回目标对象的执行结果
                return result;
            }
        });

        //在method.invoke()方法中传入的目标对象是 calculatorImpl，因此calculatorImpl这个实例化对象调用方法时都是调用的InvocationHandler.invoke()中的增强代码
        calculatorImpl.add(1,2);
    }
}

~~~

![image-20211002144300875](C:\Users\LZH\AppData\Roaming\Typora\typora-user-images\image-20211002144300875.png)

- calculatorProxyClazz.getConstructor(InvocationHandler.class); ：返回一个带参的代理类构造器
- constructor.newInstance(new InvocationHandler() {内容略})：返回代理类对象
- invoke(Object proxy, Method method, Object[] args)：在其中编写增强代码，并且在其中指定增强的目标对象
- method.invoke(calculatorImpl, args);：指定为哪个目标对象增添增强代码(本例中是 calculatorImpl)，其返回值是方法的返回值

将增强代码编写在 InvocationHandler.invoke() 方法中，实现了增强代码 和 代理对象的解耦。



### 更好用的接口 Proxy.newProxyInstance()

```java
/**
 * 本例实用Proxy.newProxyInstance(ClassLoader, Class, InvocationHandler)
 */
public class useNewProxyInstance {
    public static void main(String[] args) {
        //创建目标对象实例
        CalculatorImpl target = new CalculatorImpl();
        //获取InvocationHandler
        InvocationHandler invocationHandler = getInvocationHandler(target);
        //调用Proxy.newProxyInstance()方法，获取代理对象
        Calculator calculatorProxy = (Calculator) Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), invocationHandler);
        calculatorProxy.add(1,2);
    }


    private static InvocationHandler getInvocationHandler(final CalculatorImpl target) {
        return new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println(method.getName() + " start");
                Object result = method.invoke(target);
                System.out.println(result);
                System.out.println(method.getName() + " end");
                return result;
            }
        };
    }
}
```



### JDK 动态代理总结

前提，目标类得有一个接口

1. 使用Proxy.getClass(ClassLoader, Class)获取到接口的代理Class
2. 通过代理Class中的 getConstructor(InvocationHandler.class) 为代理Class创建一个带参的构造器
3. 调用创建好的构造器的 newInstance(new InvocationHandler(){内容}) 方法
4. 在第3步中的内容中实现 invoke() 方法，在其中编写增强代码并指定目标对象
5. 在外部实例化一个目标对象，当目标对象调用方法时，会调用代理类中的同名方法，而代理类中调用方法时会执行 invoke() 中的代码，所以目标对象调用方法时最终调用的时 invoke() 中的代码。

![zzzzz234349723479](..\img\zzzzz234349723479.png)




