## SpringBean的生命周期



## Spring的IOC和AOP机制

### IOC

> 控制反转，依赖注入，运用到了工厂设计模式

在传统的程序设计中，调用者需要被调用者协助时，通常由调用者来创建被调用者的实例。但在Spring中，对象实例的创建和管理都将由Spring框架来完成。当调用者需要用到被调用者实例时，仅需在被调用者的变量上添加对应注解即，被调用者即被注入至调用者中。

### AOP

> 面向切面编程，用到了代理设计模式

AOP通过分析程序结构的关注点来完善面向对象编程OOP，AOP相当于将程序分解成多个切面，通过动态代理，将封装好的切面注入到目标对象当中。在动态代理过程中涉及到反射，通过反射拿去到目标对象的需要代理的对应方法，然后通过 Proxy 类将封装好的切面注入至目标对象的周围。



## @Autowired和@Resource的区别

相同点：所要实现的目的都是相同的，即将Spring工厂中的实例注入进调用者当中。两者都可以写在字段和setter方法上。

### @Autowired

1. @Autowired 为 Spring 内置注解
2. @Autowired 默认 byType 装配依赖对象。当想要 byName 来装配依赖对象的话，就得配合 @Qualifier 注解。

### @Resource

1. @Resource 不是Spring中的注解
2. @Resource 默认 byName 装配依赖对象。@Resource 注解中可以声明 type 和 name 这样就可以根据需求来决定 byType 或者 byName。如果声明了 type 或 name 属性，但在工厂类中找不到时，将抛异常。



## 依赖注入的方式有几种，各是什么？

### 构造器注入

> 将被依赖对象（被调用者）通过调用者中构造函数的参数注入给依赖对象（调用者），并且在**初始化对象时**注入。

优点：对象初始化完成后便可获得可使用的对象。

缺点：当需要注入的对象很多事，构造函数的参数列表过长；不够灵活，如果需要实现多种注入方式的话，每种方式只需注入几个依赖，那么就需要创建多个不同参数的构造函数。

### setter 方法注入

> 通过调用成员变量的 setter 方法将被依赖对象注入进依赖类中

优点：灵活，可以针对不同字段选择不同的注入方式。

缺点：对象的注入在依赖对象初始化之后，因此对象初始化完成后还需等被依赖对象被注入才可使用被依赖对象。

### 接口注入

> 依赖类必须实现指定接口，然后实现接口中的一个函数，该函数就是用于依赖注入。该函数的参数就是要注入的对象

优点：接口名、函数名、参数名都不重要，只需要保证注入的对象类型可以对应上即可成功注入。

缺点：代码侵入性太高，不建议使用。



## SpringMVC的基本工作流程

### SpringMVC的组件

- DispatcherServlet：前端控制器。SpringMVC整个流程的核心，很多的请求转发都要经过它
- HandlerMapping：处理器映射器。用于处理 DispatcherServlet 转发而来的请求，根据请求返回对应的 Handler 给 DispatcherServlet。
- Handler：处理器。可以理解为 Controller
- HandlerAdapter：处理器适配器。DispatcherServlet 请求 HandlerAdapter 根据不同规则（和请求类型对应？）执行不同类型的 Handler，执行完毕后返回一个 ModelAndView 给 DispatcherServlet。
- View Resolver：视图解析器。将ModelAndView 解析，最后返回 View
- View：转交给前端渲染的视图

### SpringMVC的工作流程

1. 用户发送请求至前端控制器 DispatcherServlet。
2. DispatcherServlet 收到请求调用 HandlerMapping 处理映射器。
3. HandlerMapping 根据注解或xml文件找到具体的处理器，生成 Handler 和 HandlerIntercepter（如果有）一并返回给 DispatcherServlet
4. DispatcherServlet 调用 HandlerAdapter 处理器适配器
5. HandlerAdapter 经过适配调用具体的处理器Handler（这里的 Handler 本质上就是Controller）
6. Controller 执行完毕返回 ModelAndView
7. HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet
8. DispatcherServlet 将 ModelAndView 传给 ViewResolver 视图解析器
9. ViewResolver 将 ModelAndView 解析后返回给具体的 View
10. DispatcherServlet 根据 View 来对视图进行渲染
11. DispatcherServlet 响应用户。





## 常见面试题

#### AOP 底层原理

Spring AOP 底层是通过JDK动态代理或CGlib动态代理，默认情况是JDK动态代理

- JDK：仅为接口创建动态代理实例，而不能对类创建动态代理实例。通过反射机制获取目标类的接口信息，生成了一个代理接口的动态代理类，再通过反射机制获得动态代理类的构造函数，利用构造函数生成动态代理类的实例对象，并在调用具体方法前调用invokeHandler方法来处理目标方法（基于接口）
- CGlib：把被代理对象类的class文件加载进来，修改其字节码生成子类。（基于子类）



#### Spring Bean 的生命周期

- 读取上下文或配置中的bean扩展实现
- 通过反射创建bean实例对象
- 依赖注入创建的bean，并且会执行一些beanPostPorcessor中的扩展回调
- 将bean保存至容器当中，此时bean会创建，在此阶段会执行一些init-method和beanPostProcessor中的后置处理方法
- 销毁bean，并且回调一些定义的销毁前方法



#### Spring 的三级缓存



































