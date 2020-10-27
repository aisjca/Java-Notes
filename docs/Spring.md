# Spring目录

[什么是Spring框架，Spring框架主要包含哪些模块](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#什么是Spring框架，Spring框架主要包含哪些模块)

[Spring框架的优势](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring框架的优势)

[IOC和DI是什么？](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#ioc%E5%92%8Cdi%E6%98%AF%E4%BB%80%E4%B9%88)

[Spring IOC容器的初始化过程](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#spring-ioc%E5%AE%B9%E5%99%A8%E7%9A%84%E5%88%9D%E5%A7%8B%E5%8C%96%E8%BF%87%E7%A8%8B)

[BeanFactory 和 FactoryBean的区别](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#beanfactory-%E5%92%8C-factorybean%E7%9A%84%E5%8C%BA%E5%88%AB)

[Spring Bean 的生命周期](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#spring-bean-%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)

[Spring的IOC理解](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring的IOC理解)

[Spring的AOP理解](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring的AOP理解)

[Spring 中用到了那些设计模式](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#spring-%E4%B8%AD%E7%94%A8%E5%88%B0%E4%BA%86%E9%82%A3%E4%BA%9B%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)

[Spring如何解决循环依赖](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#spring%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96)

[Bean的作用域](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Bean的作用域)

[Spring框架中的单例Beans是线程安全的吗](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring框架中的单例Beans是线程安全的吗)

[Spring如何处理线程并发问题](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring如何处理线程并发问题)

[Spring事务的实现方式和实现原理](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring事务的实现方式和实现原理)

[Spring框架中有哪些不同类型的事件](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring框架中有哪些不同类型的事件)

[Spring的注解](https://github.com/aisjca/Java-Notes/blob/master/docs/Spring.md#Spring的注解)

# 什么是Spring框架，Spring框架主要包含哪些模块

​		Spring是一个开源框架，Spring是一个轻量级的Java 开发框架。它是为了解决企业应用开发的复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许使用者选择使用哪一个组件，同时为 J2EE 应用程序开发提供集成的框架。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益。Spring的核心是控制反转（IoC）和面向切面（AOP）。简单来说，Spring是一个分层的full-stack(一站式) 轻量级开源框架。

主要包含的模块：

![Spring结构图.png](https://github.com/aisjca/Java-Notes/blob/master/%E5%9B%BE%E7%89%87/Spring/Spring%E7%BB%93%E6%9E%84%E5%9B%BE.png?raw=true)

#### IOC

* 将类与类之间的依赖从代码中脱离出来，用配置的方式进行依赖关系描述。由Ioc负责类之间的创建，拼接，管理，获取等工作。BeanFactory接口是Spring框架的核心接口，它实现了容器许多功能

* Context模块扩展了BeanFactory功能，添加了国际化，Bean生命周期，任务调度，EJB集成等。ApplicationContext是Context的模块的核心接口
* 表达式语言模块是统一表达式的一个扩展，用于查询和管理运行时的对象，可以设置和获取对象属性等。

#### AOP

* Spring AOP是面向切面编程，它也提供了模块化。在面向对象编程中，关键的单元是对象，AOP的关键单元是切面，或者说关注点（可以简单地理解为你程序中的独立模块）。一些切面可能有集中的代码，但是有些可能被分散或者混杂在一起，例如日志或者事务。**这些分散的切面被称为横切关注点**。一个横切关注点是一个可以影响到整个应用的关注点，而且应该被尽量地集中到代码的一个地方，例如事务管理、权限、日志、安全等。
  AOP让你可以使用简单可插拔的配置，在实际逻辑执行之前、之后或周围动态添加横切关注点。这让代码在当下和将来都变得易于维护。如果你是使用XML来使用切面的话，要添加或删除关注点，你不用重新编译完整的源代码，而仅仅需要修改配置文件就可以了。
  Spring AOP通过以下两种方式来使用
  1. 使用AspectJ 注解风格
  2. 使用Spring XML 配置风格

#### 数据访问和集成

* 站在DAO的抽象层，建立一套面向Dao层的统一异常体系，可用该结构来管理异常处理，和不同数据库供应商所抛出的错误信息。异常层次结构简化了错误处理，并且极大的降低了需要编写的代码数量，比如打开和关闭链接。
* Spring框架插入了若干个ORM框架，从而提供了ORM对象的关系工具，其中包括了Hibernate、JDO和 IBatis SQL Map等，所有这些都遵从Spring的通用事物和DAO异常层次结构。

#### Web及远程操作

* 建立在Application Context上，提供Web各种工具，如果通过Listener或Servlet初始化Spring容器，将Spring容器注册到Web容器中。所以Spring框架可以整合Struts，WebWork等MVC框架。

#### MVC模块

* MVC框架是一个全功能的构建Web应用程序的MVC实现。通过策略接口，MVC框架变成为高度可配置的。MVC容纳了大量视图技术，其中包括JSP、POI等，模型来有JavaBean来构成，存放于M当中，而视图是一个街口，负责实现模型，控制器表示逻辑代码，由C的事情。Spring框架的功能可以用在任何J2EE服务器当中，大多数功能也适用于不受管理的环境。Spring的核心要点就是支持不绑定到特定J2EE服务的可重用业务和数据的访问的对象，毫无疑问这样的对象可以在不同的J2EE环境，独立应用程序和测试环境之间重用。

# Spring框架的优势

​		1、Spring通过DI、AOP和消除样板式代码来简化企业级Java开发

​		2、Spring框架之外还存在一个构建在核心框架之上的庞大生态圈，它将Spring扩展到不同的领域，如Web服务、REST、移动开发以及NoSQL

​		3、低侵入式设计，代码的污染极低

​		4、独立于各种应用服务器，基于Spring框架的应用，可以真正实现Write Once,Run Anywhere的承诺

​		5、Spring的IoC容器降低了业务对象替换的复杂性，提高了组件之间的解耦

​		6、Spring的AOP允许将一些通用任务如安全、事务、日志等进行集中式处理，从而提供了更好的复用

​		7、Spring的ORM和DAO提供了与第三方持久层框架的的良好整合，并简化了底层的数据库访问

​		8、Spring的高度开放性，并不强制应用完全依赖于Spring，开发者可自由选用Spring框架的部分或全部

# IOC和DI是什么？

​		控制反转是就是应用本身不负责依赖对象的创建和维护,依赖对象的创建及维护是由外部容器负责的,这样控制权就有应用转移到了外部容器,控制权的转移就是控制反转。

​		依赖注入是指:在程序运行期间,由外部容器动态地将依赖对象注入到组件中如：一般，通过构造函数注入或者setter注入。

# Spring IOC容器的初始化过程

​		Spring IOC容器的初始化简单的可以分为三个过程：

​		第一个过程是Resource资源定位。这个Resouce指的是BeanDefinition的资源定位。这个过程就是容器找数据的过程，就像水桶装水需要先找到水一样。

​		第二个过程是BeanDefinition的载入过程。这个载入过程是把用户定义好的Bean表示成Ioc容器内部的数据结构，而这个容器内部的数据结构就是BeanDefition。

​		第三个过程是向IOC容器注册这些BeanDefinition的过程，这个过程就是将前面的BeanDefition保存到HashMap中的过程。

# BeanFactory 和 FactoryBean的区别

- **BeanFactory**是个Factory，也就是IOC容器或对象工厂，在Spring中，所有的Bean都是由BeanFactory(也就是IOC容器)来进行管理的，提供了实例化对象和拿对象的功能。

  使用场景：

  - 从Ioc容器中获取Bean(byName or byType)
  - 检索Ioc容器中是否包含指定的Bean
  - 判断Bean是否为单例

- **FactoryBean**是个Bean，这个Bean不是简单的Bean，而是一个能生产或者修饰对象生成的工厂Bean,它的实现与设计模式中的工厂模式和修饰器模式类似。

  使用场景
  - ProxyFactoryBean
  

# BeanFactory和ApplicationContext的异同

两者关系是ApplicationContext：作为BeanFactory的派生，除了提供BeanFactory所具有的功能外，还提供了更完整的框架功能：

* 继承MessageSource，因此支持国际化
* 统一的资源文件访问方式。
* 提供在监听器中注册bean的事件。
* 同时加载多个配置文件。
* 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层。

### 两者相同点：

1. Spring提供了两种不同的IOC 容器，一个是BeanFactory，另外一个是ApplicationContext，它们都是接口，ApplicationContext继承于BeanFactory，而ApplicationContext继承于ListableBeanFactory。
2. 它们都可以用来配置XML属性，也支持属性的自动注入。
3. 而ListableBeanFactory继承BeanFactory)，BeanFactory 和 ApplicationContext 都提供了一种方式，使用getBean("bean name")获取bean。

### 两者不同点：

1.  BeanFactroy采用的是延迟加载形式来注入Bean的，即只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化。这样，我们就不能发现一些存在的Spring的配置问题。如果Bean的某一个属性没有注入，BeanFacotry加载后，直至第一次使用调用getBean方法才会抛出异常。
2. ApplicationContext，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误，这样有利于检查所依赖属性是否注入。 ApplicationContext启动后预载入所有的单实例Bean，通过预载入单实例bean ,确保当你需要的时候，你就不用等待，因为它们已经创建好了。
3. 相对于基本的BeanFactory，ApplicationContext 唯一的不足是占用内存空间。当应用程序配置Bean较多时，程序启动较慢。
4. BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如使用ContextLoader。
5. BeanFactory和ApplicationContext都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是：BeanFactory需要手动注册，而ApplicationContext则是自动注册。

# Spring Bean 的生命周期

这个是BeanFactory中Bean生命周期，**带*号的代表是接口**。

![Bean生命周期.png](https://github.com/aisjca/Java-Notes/blob/master/%E5%9B%BE%E7%89%87/Spring/Bean%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png?raw=true)

总结：

**1实例化Bean：**

* 对于BeanFactory容器，当客户向容器请求一个尚未初始化的bean时，或初始化bean的时候需要注入另一个尚未初始化的依赖时，容器就会调用createBean进行实例化。

* 对于ApplicationContext容器，当容器启动结束后，通过获取BeanDefinition对象中的信息，实例化所有的bean。

**2 对Bean初始化：**

1. 如果容器注册了InstantiationAwareBeanPostProcessorAdapter接口，就调用postProcessAfterInitialization()对实例化Bean初始化。
2. 如果Bean配置了属性信息，则调用InstantiationAwareBeanPostProcessorAdapter.postProcessPropertiesValues()方法

**3 处理Aware接口：**

接着，Spring会检测该对象是否实现了xxxAware接口，并将相关的xxxAware实例注入给Bean：

* 对于BeanFactory容器如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String beanId)方法，此处传递的就是Spring配置文件中Bean的id值；

  如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory()方法，传递的是Spring工厂自身。

* 而对于ApplicationContext容器，会比BeanFactory多了一步，就是如果是在如果这个Bean已经实现了ApplicationContextAware接口，会调用setApplicationContext(ApplicationContext)方法，传入Spring上下文；

**4 BeanPostProcessor：**

如果想对Bean进行一些自定义的处理，那么可以让Bean实现了BeanPostProcessor接口，调用BeanPostProcessor.postProcessBeforeInitialization()方法。

**5 InitializingBean 与 init-method：**

如果Bean实现了InitializingBean接口，则调用afterPropertiesSet()

如果\<bean>配置文件中配置了 init-method 属性，则会自动调用其配置的初始化方法。

**6 如果这个Bean实现了BeanPostProcessor接口**，

将会调用postProcessAfterInitialization(Object obj, String s)方法；由于这个方法是在Bean初始化结束时调用的，所以可以被应用于内存或缓存技术；

以上几个步骤完成后，Bean就已经被正确创建了，之后就可以使用这个Bean了。

**（7）DisposableBean：**

当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用其实现的destroy()方法；

**（8）destroy-method：**

最后，如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法。

# Spring的IOC理解

1. IOC就是控制反转，是指创建对象的控制权的转移，以前创建对象的主动权和时机是由自己把控的，而现在这种权力转移到Spring容器中，并由容器根据配置文件去创建实例和管理各个实例之间的依赖关系，对象与对象之间松散耦合，也利于功能的复用。DI依赖注入，和控制反转是同一个概念的不同角度的描述，即 应用程序在运行时依赖IoC容器来动态注入对象需要的外部资源。
2. 最直观的表达就是，IOC让对象的创建不用去new了，可以由spring自动生产，使用java的反射机制，根据配置文件在运行时动态的去创建对象以及管理对象，并调用对象的方法的。
3. Spring的IOC有三种注入方式 ：构造器注入、setter方法注入、根据注解注入。

# Spring的AOP理解

OOP面向对象，允许开发者定义纵向的关系，但并适用于定义横向的关系，导致了大量代码的重复，而不利于各个模块的重用。

AOP，一般称为面向切面，作为面向对象的一种补充，用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被命名为“切面”（Aspect），减少系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。可用于权限认证、日志、事务处理。

AOP实现的关键在于 代理模式，AOP代理主要分为静态代理和动态代理。静态代理有AspectJ；动态代理则有JDK动态代理和CGLIB动态代理。

（1）AspectJ是静态代理的增强，所谓静态代理，就是AOP框架会在编译阶段生成AOP代理类，因此也称为编译时增强，他会在编译阶段将AspectJ(切面)织入到Java字节码中，运行的时候就是增强之后的AOP对象。

（2）Spring AOP使用的动态代理，所谓的动态代理就是说AOP框架不会去修改字节码，而是每次运行时在内存中临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。

### Spring AOP中的动态代理主要有两种方式

####  JDK动态代理

JDK动态代理只提供接口的代理，不支持类的代理。核心InvocationHandler接口和Proxy类，InvocationHandler 通过invoke()方法反射来调用目标类中的代码，动态地将横切逻辑和业务编织在一起；接着，Proxy利用 InvocationHandler动态创建一个符合某一接口的的实例, 生成目标类的代理对象。

#### CGLIB动态代理

如果代理类没有实现 InvocationHandler 接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成指定类的一个子类对象，并覆盖其中特定方法并添加增强代码，从而实现AOP。CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。

### 静态代理与动态代理区别

静态代理与动态代理区别在于生成AOP代理对象的时机不同，相对来说AspectJ的静态代理方式具有更好的性能，但是AspectJ需要特定的编译器进行处理，而Spring AOP则无需特定的编译器处理

### Spring 切面可以应用 5 种类型的通知

前置通知（Before）：在目标方法被调用之前调用通知功能；

后置通知（After）：在目标方法完成之后调用通知，此时不会关心方法的输出是什么；

返回通知（After-returning）：在目标方法成功执行之后调用通 知；

异常通知（After-throwing）：在目标方法抛出异常后调用通知；

环绕通知（Around）：通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为。

aop是把与主要业务无关的代码，抽离出来解耦，如日志执行时间，权限，回滚事务

cglib java动态代理通过字节码来实现

# Spring 中用到了那些设计模式

- 代理模式—在AOP中被用的比较多。
- 单例模式—在spring配置文件中定义的bean默认为单例模式。
- 模板方法—用来解决代码重复的问题。比如. RestTemplate, JmsTemplate, JpaTemplate。
- 工厂模式—BeanFactory用来创建对象的实例。
- 适配器--spring aop
- 装饰器--spring data hashmapper
- 观察者-- spring 事件驱动模型
- 回调--Spring Aware回调接口

# Spring如何解决循环依赖

- Spring是通过递归的方式获取目标bean及其所依赖的bean的；
- Spring实例化一个bean的时候，是分两步进行的，首先实例化目标bean，然后为其注入属性。

# Bean的作用域

1. singleton：默认，每个容器中只有一个bean的实例，单例的模式由BeanFactory自身来维护。
2. prototype：为每一个bean请求提供一个实例。
3. request：为每一个网络请求创建一个实例，在请求完成以后，bean会失效并被垃圾回收器回收。
4. session：与request范围类似，确保每个session中有一个bean的实例，在session过期后，bean会随之失效。

# Spring框架中的单例Beans是线程安全的吗

Spring框架并没有对单例bean进行任何多线程的封装处理。关于单例bean的线程安全和并发问题需要开发者自行去搞定。但实际上，大部分的Spring bean并没有可变的状态(比如Serview类和DAO类)，所以在某种程度上说Spring的单例bean是线程安全的。如果你的bean有多种状态的话（比如 View Model 对象），就需要自行保证线程安全。最浅显的解决办法就是将多态bean的作用域由“singleton”变更为“prototype”。

# Spring如何处理线程并发问题

在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作用域，因为Spring对一些Bean中非线程安全状态**采用ThreadLocal进行处理，解决线程安全问题。**

ThreadLocal和线程同步机制都是为了解决多线程中相同变量的访问冲突问题。同步机制采用了“时间换空间”的方式，仅提供一份变量，不同的线程在访问前需要获取锁，没获得锁的线程则需要排队。而ThreadLocal采用了“空间换时间”的方式。

ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。

# Spring事务的实现方式和实现原理

### Spring 是如何管理事务的？

Spring事务管理主要包括3个接口，Spring的事务主要是由它们(**PlatformTransactionManager，TransactionDefinition，TransactionStatus**)三个共同完成的。

**1. PlatformTransactionManager**：事务管理器--主要用于平台相关事务的管理

主要有三个方法：

- commit 事务提交；
- rollback 事务回滚；
- getTransaction 获取事务状态。

**2. TransactionDefinition**：事务定义信息--用来定义事务相关的属性，给事务管理器PlatformTransactionManager使用

这个接口有下面四个主要方法：

- getIsolationLevel：获取隔离级别；
- getPropagationBehavior：获取传播行为；
- getTimeout：获取超时时间；
- isReadOnly：是否只读（保存、更新、删除时属性变为false--可读写，查询时为true--只读）

事务管理器能够根据这个返回值进行优化，这些事务的配置信息，都可以通过配置文件进行配置。

**3. TransactionStatus**：事务具体运行状态--事务管理过程中，每个时间点事务的状态信息。

例如它的几个方法：

- hasSavepoint()：返回这个事务内部是否包含一个保存点，
- isCompleted()：返回该事务是否已完成，也就是说，是否已经提交或回滚
- isNewTransaction()：判断当前事务是否是一个新事务

**声明式事务的优缺点**：

- **优点**：不需要在业务逻辑代码中编写事务相关代码，只需要在配置文件配置或使用注解（@Transaction），这种方式没有侵入性。
- **缺点**：声明式事务的最细粒度作用于方法上，如果像代码块也有事务需求，只能变通下，将代码块变为方法。

Spring事务的本质其实就是数据库对事务的支持，没有数据库的事务支持，spring是无法提供事务功能的。真正的数据库层的事务提交和回滚是通过binlog或者redo log实现的。

### **Spring事务的种类**

spring支持编程式事务管理和声明式事务管理两种方式：

1 编程式事务管理使用TransactionTemplate。

2 声明式事务管理建立在AOP之上的。其本质是通过AOP功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中，也就是在目标方法开始之前加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。

#### 声明式事务最大的优点

声明式事务最大的优点就是不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明或通过@Transactional注解的方式，便可以将事务规则应用到业务逻辑中。

声明式事务管理要优于编程式事务管理，这正是spring倡导的非侵入式的开发方式，使业务代码不受污染，只要加上注解就可以获得完全的事务支持。唯一不足地方是，最细粒度只能作用到方法级别，无法做到像编程式事务那样可以作用到代码块级别。

### spring的事务传播行为

1. PROPAGATION_REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，该设置是最常用的设置。
2. PROPAGATION_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。‘
3. PROPAGATION_MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。
4. PROPAGATION_REQUIRES_NEW：创建新事务，无论当前存不存在事务，都创建新事务。
5.  PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
6.  PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。
7. PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。

### Spring中的隔离级别

1.  ISOLATION_DEFAULT：这是个 PlatfromTransactionManager 默认的隔离级别，使用数据库默认的事务隔离级别。
2.  ISOLATION_READ_UNCOMMITTED：读未提交，允许另外一个事务可以看到这个事务未提交的数据。
3. ISOLATION_READ_COMMITTED：读已提交，保证一个事务修改的数据提交后才能被另一事务读取，而且能看到该事务对已有记录的更新。
4. ISOLATION_REPEATABLE_READ：可重复读，保证一个事务修改的数据提交后才能被另一事务读取，但是不能看到该事务对已有记录的更新
5. ISOLATION_SERIALIZABLE：可串行化，一个事务在执行的过程中完全看不到其他事务对数据库所做的更新。

# Spring框架中有哪些不同类型的事件

1. 上下文更新事件（ContextRefreshedEvent）：在调用ConfigurableApplicationContext 接口中的refresh()方法时被触发。
2.  上下文开始事件（ContextStartedEvent）：当容器调用ConfigurableApplicationContext的Start()方法开始/重新开始容器时触发该事件。
3. 上下文停止事件（ContextStoppedEvent）：当容器调用ConfigurableApplicationContext的Stop()方法停止容器时触发该事件。
4. 上下文关闭事件（ContextClosedEvent）：当ApplicationContext被关闭时触发该事件。容器被关闭时，其管理的所有单例Bean都被销毁。
5. 请求处理事件（RequestHandledEvent）：在Web应用中，当一个http请求（request）结束触发该事件。

如果一个bean实现了ApplicationListener接口，当一个ApplicationEvent 被发布以后，bean会自动被通知

# Spring的注解

### @Required 

这个注解表明bean的属性必须在配置的时候设置，通过一个bean定义的显式的属性值或通过自动装配，若@Required注解的bean属性未被设置，容器将抛出BeanInitializationException

### @Component

这将 java 类标记为 bean。它是任何 Spring 管理组件的通用构造型。spring 的组件扫描机制现在可以将其拾取并将其拉入应用程序环境中。

### @Controller

这将一个类标记为 Spring Web MVC 控制器。标有它的 Bean 会自动导入到 IoC 容器中。

### @Service

此注解是组件注解的特化。它不会对 @Component 注解提供任何其他行为。您可以在服务层类中使用 @Service 而不是 @Component，因为它以更好的方式指定了意图

### @Autowired 

@Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）。@Autowired 注解提供了更细粒度的控制，包括在何处以及如何完成自动装配。它的用法和@Required一样，修饰setter方法、构造器、属性或者具有任意名称和/或多个参数的PN方法


### @Autowired和@Resource之间的区别

@Autowired可用于：构造函数、成员变量、Setter方法

@Autowired和@Resource之间的区别

@Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）。

@Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入。


### @Qualifier

当您创建多个相同类型的 bean 并希望仅使用属性装配其中一个 bean 时，您可以使用@Qualifier 注解和 @Autowired 通过指定应该装配哪个确切的 bean 来消除歧义。

### @RequestMapping

注解用于将特定 HTTP 请求方法映射到将处理相应请求的控制器中的特定类/方法。此注释可应用于两个级别

* 类级别：映射请求的 URL
* 方法级别：映射 URL 以及 HTTP 请求方法

### **@Retention**：

注解的保留位置　　　　　　　　　

**@Retention(RetentionPolicy.SOURCE)**  //注解仅存在于源码中，在class字节码文件中不包含

**@Retention(RetentionPolicy.CLASS)**   // 默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得，

**@Retention(RetentionPolicy.RUNTIME)** // 注解会在class字节码文件中存在，在运行时可以通过反射获取到

### **@Target**

注解的作用目标

**@Target(ElementType.TYPE)**  //接口、类、枚举、注解

**@Target(ElementType.FIELD)** //字段、枚举的常量

**@Target(ElementType.METHOD)** //方法

**@Target(ElementType.PARAMETER)** //方法参数

**@Target(ElementType.CONSTRUCTOR)** //构造函数

**@Target(ElementType.LOCAL_VARIABLE)**//局部变量

**@Target(ElementType.ANNOTATION_TYPE)**//注解

**@Target(ElementType.PACKAGE)** ///包 　　

