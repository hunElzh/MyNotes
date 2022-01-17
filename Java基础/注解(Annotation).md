## 注解概述

### 格式

~~~ java
public @interface MyAnnotation{
    String getValue();
}
~~~

### 分类

- 自定义注解（比如刚刚定义的@MyAnnotation）
- JDK自带注解（@Override）
- 第三方框架定义注解（@Controller）

### 使用位置

在实际开发过程中，注解常常出现在类、方法、成员变量、形参等等位置

### 作用

如果说注释时写给人看的，那么注解就是写个程序看的。注解更像是一个标签，它贴在一个类、一个方法、或者一个字段之上。它的目的是为当前读取该注解的程序提供判断依据以及少量附加信息。

比如当一个程序督导加了@Test注解的方法，就知道这个方法是一个待测试的方法；又比如当一个程序读取到@Before注解，就知道这个注解方法要放在@Test方法之前执行。

有时候我们还可以通过注解属性，为将来读取这个注解的程序提供必要的附加信息，比如@RequestMapping("/user/info")，则这个注解就为Controller提供了某个接口的URL路径

### 级别

在IDEA中创建一个类的时候，我们可以看到由一下这几个选项，说明这几个选项级别一样，因此注解的级别等同于类、接口、枚举以及Javafx。

![53HWN~0~KUT9NMB$RSGOK}Y](..\img\53HWN~0~KUT9NMB$RSGOK}Y.png)



## 注解的本质

我们将刚才的注解编译成.class文件，再将其反编译，得到如下图片（博客上拿的）

![v2-eb80156703d811f8d57c11840272f01b_b](..\img\v2-eb80156703d811f8d57c11840272f01b_b.png)



我们发现 @Interface 变成了 interface，而且自动继承了 Annotation 接口，因此注解的本质其实就是一个继承了Annotation 接口的接口，因此我们可以在这个接口中定义方法。

![zzzzz203797293](..\img\zzzzz203797293.png)

上图中，我们可以看到接口的基本实用（作用于类、方法、字段、形参），奇怪的事情出现了，getValue在注解中的样子明明是一个方法，那我们为什么还能给getValue()赋值呢？因此在注解中，类似于String getValue()这样式的，被称为 “属性”，而给属性赋值这种说法，就显得合理了起来。

如果在注解中定义了一个属性，如果没对这个属性设置默认值的话，那我们使用注解时就必须为其赋值，否则将会编译报错。

![8D_B807S3QZXV91TLG@K1JY](..\img\8D_B807S3QZXV91TLG@K1JY.png)

![zzzz12374301](..\img\zzzz12374301.png)

![zzzzzz13457324](..\img\zzzzzz13457324.png)

由此可见，注解的 “属性” 必须得被赋值，否则代码无法编译。基于以上原因，我们还是不要将注解与接口进行类比，将注解单独归为一类，不要将其当成接口使用。



## 通过反射获取注解信息

在注解概述中我们提到，注解就是贴在程序代码上供另一个程序读取的标签，所以注解、写注解的代码、读注解的代码它们三者的关系如下图所示

![zzzzf98498167](..\img\zzzzf98498167.png)

要牢记，只要用到注解，就必然有着三角关系：

1. **定义**注解
2. **使用**注解
3. **读取**注解



定义注解和使用注解我们在上文中已经实现了，现在我们需要实现的功能是——读取注解。接下来我们编写一个程序来读取注解，当我们用 “反射” 得到 Class、Method、Field后，它们都存在一个方法——getAnnotation()，我们编写下面这段程序，这样我们就可以获取到作用于类、方法、字段上面的注解了吗？

![zzzzz2397s976fv98f](..\img\zzzzz2397s976fv98f.png)

为什么会出现这种情况啊，我注解明明已经定义了，但是通过反射我还是啥都没拿到？

这里就要提到一个概念——保留策略。注解通过保留策略，控制自己可以保留到哪个阶段。保留策略也是通过注解实现的，它属于元注解。也正是因为没有对元注解的值进行设置，所以才导致上面这种情况的发生，接下来就来说道说道”元注解“是个啥玩楞。



## 元注解

> 所谓元注解，就是加载注解上的注解。

作为普通程序员，我们常见的注解是：

- @Documented：用于制作文档。
- @Target：加载注解上，用于限定注解的使用位置，没有此元注解，注解则可以作用在各个位置。
  - ElementType.TYPE：作用在类上
  - ElementType.FIELD：作用在字段上
  - ElementType.METHOD：作用在方法上
- @Retention：注解的保留策略（本节重要知识点），通过下面三个参数的解释，我们可以知道，如果需要通过反射读取注解，那就保留策略就不能设置为RetentionPolicy.CLASS和RetentionPolicy.SOURCE
  - RetentionPolicy.CLASS：在注解被编译成.class文件时仍旧存在
  - RetentionPolicy.RUNTIME：在运行时注解仍旧存在，此时可被其他类读取
  - RetentionPolicy.SOURCE：注解仅在源码阶段存在，当代码被编译成.class文件之后注解就会消失



现在我们知道了，只要将注解的保留策略设置成为RetentionPolicy.RUNTIME的话，我们就可以通过反射读取到注解中的信息了！

<img src="..\img\zzzzzfd7b99f19q7.png" alt="zzzzzfd7b99f19q7" style="float: LEFT" />

![zzzzz9r7d9f5qwe97](..\img\zzzzz9r7d9f5qwe97.png)



## 属性的数据类型以及特别的属性：value和数组

### 属性的数据类型

- 八种基本数据类型（short、int、long、float、double、char、boolean、byte）
- String
- 枚举（enum）
- Class
- 注解类型
- 以上类型的一维数组

下面这张图就展现了注解属性的数据类型的概括

![H_KQ6BS15SPR14R2WJO](..\img\H_KQ6BS15SPR14R2WJO.png)



### 两个特殊属性

#### value属性

> 如果注解的属性只有一个，并且叫value()，那么在使用该注解时，可以不用指定属性名，因为默认就是给value属性赋值

如下图所示，如果注解中只有一个属性，并且属性名为 value 时，此时使用该注解时就不需要声明属性名，直接输入属性值默认赋值给注解中的属性。

![EX0`UNFRB6WL@28QGAZB](..\img\EX0`UNFRB6WL@28QGAZB.png)

#### 数组属性

> 如果数组的属性元素只有一个，可以省略大括号{}

![I$MBPEH9GN8OQ`R48_HDK](..\img\I$MBPEH9GN8OQ`R48_HDK.png)



### 用public static final修饰注解属性

> 当用public static final修饰注解属性时，该注解的属性可以被当作值被其他注解引用（下例是定时任务的格式）

![GB_UQ3W_IMVYOP8V_@BM](..\img\GB_UQ3W_IMVYOP8V_@BM.png)





## 巩固注解使用，山寨Junit和JPA

### 山寨Junit

> 牢记定义注解、使用注解、读取注解这个三角结构，用到注解的地方必然存在三角关系

#### 代码结构

![zzzzzz3cv16as4f61a61d](..\img\zzzzzz3cv16as4f61a61d.png)

#### 案例代码

1. 定义注解——三个枚举类

~~~ java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyBefore {
}
~~~

~~~ java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyTest {
}
~~~

~~~ java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyAfter {
}
~~~

2. 使用注解


~~~ java
public class EmployeeDAOTest {

    @MyBefore
    public void init() {
        System.out.println("init......");
    }

    @MyAfter
    public void destroy() {
        System.out.println("destory......");
    }

    @MyTest
    public void testSave() {
        System.out.println("test save......");
    }

    @MyTest
    public void testDelete() {
        System.out.println("test delete......");
    }

}
~~~

3. 读取注解


~~~ java
public class MyJunitFrameWork {
    public static void main(String[] args) throws Exception, InstantiationException {
        //1. 找到测试类的字节码：EmoployeeDAOTest
        Class clazz = EmployeeDAOTest.class;
        Object obj = clazz.newInstance();

        //2. 获取EmployeeDAOTest类中所有的公共方法
        Method[] methods = clazz.getMethods();

        //3. 迭代出每一个Method对象，判断哪些方法上使用了@MyBefore/@MyAfter/@MyTest注解
        List<Method> myBeforeList = new ArrayList<>();
        List<Method> myTestList = new ArrayList<>();
        List<Method> myAfterList = new ArrayList<>();
        //遍历所有方法，当方法上的注解对应上时，就将其存入对应 ArrayList 集合中
        for (Method method : methods) {
            if (method.isAnnotationPresent(MyBefore.class)) {
                myBeforeList.add(method);
            }
            if (method.isAnnotationPresent(MyTest.class)) {
                myTestList.add(method);
            }
            if (method.isAnnotationPresent(MyAfter.class)) {
                myAfterList.add(method);
            }
        }

        //执行方法测试，每次运行@Test都要运行@Before和@After
        for (Method testMethod : myTestList) {
            // 先执行@MyBefore的方法
            for (Method beforeMethod : myBeforeList) {
                beforeMethod.invoke(obj);
            }
            // 测试方法
            testMethod.invoke(obj);
            // 最后执行@MyAfter的方法
            for (Method afterMethod : myAfterList) {
                afterMethod.invoke(obj);
            }
            System.out.println("--------------");
        }
    }
}
~~~

#### 运行结果

与我们预期的运行结果一致，当@Test注解周围都会存在初始化和销毁方法

![TD88%DV8R_4T9XMZH](..\img\TD88%DV8R_4T9XMZH.png)























































