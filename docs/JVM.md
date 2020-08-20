# Java的跨平台性

##### 相关指令

javac xxx.java 可以得到 xxx.class文件

java xxx 便可以编译这个文件得出结果

 javap -help 查看相关指令

 javap -c xxx 查看相关反汇编的源码

##### 如何实现平台无关性

1各种不同的平台的虚拟机都使用统一的程序存储格式——字节码。而且Java虚拟机只与由自己码组成的Class文件进行交互。

2因为javaclass可以再任何平台创建，也可以被任何平台的Java虚拟机装载并执行，所以才有了Java的平台无关性。

3 java中基本数据类型的值域和行为都是由其自己定义的，而C/C++中，基本数据类型是由它的占位宽度决定的，占位宽度则是由所在平台决定的。例 在Java中，int占4个字节，但是在C++中却不是固定的了。在16位计算机上，int类型的长度可能为两字节；在32位计算机上，可能为4字节

![]([https://github.com/aisjca/Java-Notes/blob/master/%E5%9B%BE%E7%89%87/JVM%E5%9B%BE/JVM%E7%BB%93%E6%9E%84.jpg](https://github.com/aisjca/Java-Notes/blob/master/图片/JVM图/JVM结构.jpg))

# 反射

```java
package 反射;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
public class ReflectSample {
    public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
        Class rc=Class.forName("反射.Robot");
        Robot r = (Robot) rc.newInstance();
        //得到反射的类的名字
        System.out.println(rc.getName());


        //可以直接为反射的类的公有方法赋值，和调用它的公有方法
        r.n = "Bod";
        r.sayHi("Robt");


        //得到反射的类的其中一个私有方法
        Method getHello = rc.getDeclaredMethod("throwHi", String.class);
        getHello.setAccessible(true);
        Object str = getHello.invoke(r, "Bod");
        System.out.println("getHello is "+str);

        //得到反射类的公有方法
        Method sayHi = rc.getMethod("sayHi", String.class);
        sayHi.invoke(r, "Hello");

        //得到反射的类的其中一个私有变量赋值
        Field name = rc.getDeclaredField("name");
        name.setAccessible(true);
        name.set(r,"Alice");
        sayHi.invoke(r, "Welcome");
    }
}

```

```java
package 反射;
public class Robot {
    private String name;
    public String n;
    public void sayHi(String helloSentence) {
        System.out.println(helloSentence + " " + name);
    }
    private String throwHi(String tag) {
        return "Hello " + tag;
    }
}
```



# JVM结构图

[https://github.com/aisjca/java-notes/blob/master/JVM%E5%9B%BE/JVM%E7%BB%93%E6%9E%84.jpg](https://github.com/aisjca/java-notes/blob/master/JVM图/JVM结构.jpg)



线程私有： 程序计算器，虚拟机栈，本地方法栈

线程共享：MetaSpace ，java堆

# 参数类型

##### 标配参数

jps -l 查看当前运行的进程数



##### x参数

Xint 解释执行

Xcomp 第一次使用就编译成本地代码

Xmixed 混合模式

##### xx参数

查看元空间  jinfo -flag MetaspaceSize  xxx(进程号)

查看养老区  jinfo -flag MaxTenuringThreshold  xxx(进程号)

查看GC详细信息  jinfo -flag PrintGCDetails  xxx(进程号)

查看所有详细信息  jinfo -flags  xxx(进程号)



##### 调优相关参数查看

查看所有参数信息  java -XX:+PrintFlagsInitial

查看修改后的值 java -XX:+PrintFlagsFinal -version

修改某些参数值 java -XX:+PrintFlagsFinal  -XX: MetaspaceSize=512m T

查看gc相关信息 java -XX:+PrintCommandLineFlags -version



##### jvm常用参数

在run-> Edit Configurations-> VM options 设置该参数 

[https://github.com/aisjca/java-notes/blob/master/JVM%E5%9B%BE/%E8%B0%83%E5%8F%82.png](https://github.com/aisjca/java-notes/blob/master/JVM图/调参.png)

-Xms  初始堆的大小，默认物理内存1/64 等价于 -XX:InitialHeapSize

-Xmx 初始化堆能达到的最大值，默认物理内存1/4 等价于 -XX:MaxHeapSize

-Xss  设置单个线程栈虚拟机栈的大小，一般默认为512k~1024k 等价于-XX:ThreadStackSize

-Xmn 设置年轻代大小

-XX:+PrintGCDetails 查看GC详细信息新生代和老年代相关信息 

-XX:SurvivorRatio 设置新生代中eden和S0，S1空间的比例

​							默认 -XX:SurvivorRatio=8,Eden:S0:S1=8:1:1

-XX:NewRatio  配置年轻代和老年代在堆结构的占比

​							默认-XX:NewRatio=2新生代占1，老年代占2，年轻代占整个堆1/3	 

​							NewRatio值就是设置老年代的占比，剩下的1给新生代

-XX: MaxTenuringThreshold 设置垃圾最大年龄

# 类加载器

负责加载class文件内容到内存中。并将内存转换到方法区中

1 启动类加载器(BootstrapClassLoader) c++编写 加载核心库java.*

2扩展类加载器(ExClassLoader)java编写 加载扩展库javax.*

3应用程序类加载器(AppClassLoader) java编写，加载程序所在目录

4自定义ClassLoader java编写，自定义加载

##### 双亲委派机制

[https://github.com/aisjca/java-notes/blob/master/JVM%E5%9B%BE/%E5%8F%8C%E4%BA%B2%E5%A7%94%E6%B4%BE%E6%9C%BA%E5%88%B6.jpeg](https://github.com/aisjca/java-notes/blob/master/JVM图/双亲委派机制.jpeg)





当一个类收到类加载请求后，它首先不会自己尝试加载这个类，而是把这个请求交给父类，每一层加载类都是这样，会一直从自定义类加载器往上找，只有当父类加载器反馈自己无法完成的时候，子类加载器才会去加载。

例如：因为java自带String类，如果自己写了个父类的话，类加载器会找到java自带的String类，为了保证User编写的代码不污染java自带的代码，因此就有了双亲委派机制，保证了安全，确保使用不同的类加载器最终得到的都是同一个object对象。

##### 为什么使用双亲委派机制

防止多份同样字节码的加载



#### ClassForname和ClassLoader区别

在java中Class.forName()和ClassLoader都可以对类进行加载。

ClassLoader就是遵循**双亲委派模型**最终调用启动类加载器的类加载器，实现的功能是“通过一个类的全限定名来获取描述此类的二进制字节流”，获取到二进制流后放到JVM中。Class.forName()方法实际上也是调用的CLassLoader来实现的。

Class.forName加载类时将类进了初始化(如静态代码块那些也会运行)，而ClassLoader的loadClass并没有对类进行初始化，只是把类加载到了虚拟机中。

# Native方法

调用与java无关的操作系统库和底层的c语言库

# 程序计数器

1 当前线程所指向的字节码行号指示器

2 改变计数器的值选取下一条需要执行的字节指令

3 和线程一对一的关系即 有线程私有

4 和java方法计数，如果是native方法则计数器值为Undefined

5 不会内存泄漏

# PC寄存器

每个线程都有一个程序计数器，是线程私有的，就是一个指针，指向方法区中的方法字节码，用来存储指向下一条指令，也即将要执行的指令代码，由执行引擎，内存很小，基乎可以忽略。

它是当前线程锁执行的字节码的型号指示器



# 方法区

供各线程共享的运行时内存区域，它存储每一个类的结构信息，例如常量池，字段，方法数据，构造方法，普通方法。上面讲的是规范，在不同虚拟机实现是不一样的，最典型的就是永久代和元空间。

方法区 f=new 永久代

方法区 f=new 元空间

# 栈stack

1 java方法执行的内存模型

2 包含多个栈帧

栈管运行，堆管存储

线程私有，没有垃圾回收机制，里面存的是八种数据类型，对象的引用变量，实例方法都是在函数的栈内存中分配

方法=栈帧

# 堆head

1.6以前字符串常量池在永久代，1.6以后字符串常量池在堆中

1 新生区

1. Eden区(伊甸区)
2. 幸存者0区
3. 幸存者1区

2 老年代

3 元空间

**OOM产生的原因**

原理 ：新生区是类诞生，成长，消亡的过程，一个类在这产生，最后被垃圾回收器收集，结束生命。新生区又分为两部分：伊甸区和幸存者区，所有的类在伊甸区被new出来，幸存者有两个区，一个是0区，一个是1区，当伊甸空间用完，程序创建新的对象，JVM垃圾回收器将对伊甸园进行垃圾回收 Minor GC，将伊甸园区的不再被其他对象所引用的对象进行销毁，然后将伊甸园的剩余对象移动到幸存者0区，若幸存者0区也满了，再对该区进行垃圾回收，然后移动到幸存者1区，若1区再满了，再移动到养老区，这个时候就会产生MajorGC(FullGC)，

进行养老区的内存清理。若养老区FUll GC后还是无法有足够空间保存对象，则发生OOM

(OutOfMemoryError)

##### 堆结构图

[https://github.com/aisjca/java-notes/blob/master/JVM%E5%9B%BE/%E5%A0%86%E7%BB%93%E6%9E%84.png](https://github.com/aisjca/java-notes/blob/master/JVM图/堆结构.png)

新生代占总的1/3 ,老年代2/3;

新生代里面默认比例 Eden:From:To=8:1:1

(MinorGC过程)

1 eden，SurvivorFrom，SurvivorTo

首先eden区满了后，触发第一次gc，然后把还活着的对象放到From区，当eden再次出发gc的时候，这次gc会扫描eden和from，对这两个区域进行垃圾回收，经过这次回收还有存活的对象，则直接复制到To区域，(如果对象已经到达老年代的标准，则赋值到老年区)同时对着年龄加一

2 清空eden，SurvivorFrom

然后清空eden，和SurvivorFrom的对象，也即是复制后就是交换，谁空谁是TO

3 SurvivorTo和 SurvivorFrom交换

最后SurvivorTo和 SurvivorFrom交换，原 SurvivorTo成为下一次GC时的 SurvivorFrom区。部分对象会在From和To区域互相复制，如果交换15次后(由jvm参数MaxTenuringThreshold决定，默认值为15，)最终还是存活的话，就存入老年代。

##### java8与java7的区别

java8永久代移除了，被元空间取代了，但是元空间本质和永久代差不多。

元空间和永久代最大区别是：永久代使用jvm堆内存，但是java8以后元空间不在虚拟机，而是使用本机的物理内存，元空间仅受本地内存大小限制

##### 堆内存调优

-Xms 设置初始分配值，默认为物理内存的1/64

-Xmx 最大分配内存，默认为物理内存的1/4 

最好设置Xms和Xmx一样大

```java
public class Main {
    public static void main(String[] args) {
        long MaxMemory = Runtime.getRuntime().maxMemory();
        long totalMemory = Runtime.getRuntime().totalMemory();
        System.out.println(MaxMemory+"字节"+" "+MaxMemory/(double)1024/1024+"MB");
        System.out.println(totalMemory+"字节"+" "+totalMemory/(double)1024/1024+"MB");
    }
}
//查看当前堆分配的内存
```



# 堆和栈的区别

管理方式：栈自动释放，堆要GC

空间大小：栈比堆小

碎片相关：栈产生的碎片远小于堆

分配方式：栈支持静态和动态分配，而堆是支持动态分配

效率：栈的效率比堆高

# JMM

这是java内存模型，是一种抽象的概念，并不真实存在，描述是一组规范或规避，通过这组规范定义了程序中各个变量



JMM关于同步的规定

1. 线程解锁前  必须把共享变量的值刷新回主内存
2. 线程加锁前，必须读取主内存的最新在到自己工作内存
3. 加锁解锁是同一把锁



由于JVM 运行程序的实体是线程，每个线程创建时，JVM创建一个工作内存，工作内存是每个线程的私有数据区域，而java内存模型规定所有遍历都存**主内存**，主内存共享内存区域，所有线程可以访问，**但线程对变量的操作必须在工作内存才行，首先要把变量从主内存拷贝到自己的工作内存中，然后对变量进行操作，操作完成后再将变量写回去主内存。**不能直接操作主内存的变量，各个线程存储着主内存的变量副本拷贝，因此不同的线程间无法访问对方的工作内存，线程间通信必须通过主内存完成。

。

# 垃圾回收算法

##### gc收集日志信息解释图

[https://github.com/aisjca/java-notes/blob/master/JVM%E5%9B%BE/gc.jpg](https://github.com/aisjca/java-notes/blob/master/JVM图/gc.jpg)

##### gc是什么(分代收集算法)

次数上频繁收集young区，次数上较少收集old区，基本不动元空间

##### 引用计数原理

1 首先判断对象引用数量来决定对象是否可以被回收

2 每个对象实例都有一个引用计算器，被引用+1，完成引用-1；

3 任何引用计数为0的对象实例可以被当做垃圾收集

优点 执行效率高，程序执行受影响较小

缺点 无法检测出循环引用情况，导致内存泄露(**程序在向系统申请分配内存空间后(new)，在使用完毕后未释放。**结果导致一直占据该内存单元，我们和程序都无法再使用该内存单元，直到程序结束，这是内存泄露)

#### 4种垃圾回收算法

##### 复制

年轻代的垃圾回收算法使用复制算法从根集合GC Root开始，通过Tracing从From中找到存活对象，拷贝到To中，From，To交换身份，下次内存分配从To开始。**复制算法不会产生内存碎片，但是耗空间**。

在GC开始的时候，对象只会存在于Eden区和名为From的Suvivor区，Suvivor区To是空的。然后Eden所有存活的对象都会被复制到To，而在From区中，扔存活的对象会根据他们的年龄来决定去向，年龄到达一定的值的对象就会被移动到老年代(可以通过 -XX:MaxTenuringThreshold来设置)，没到达阈值的对象会被复制到To区

经过这次GC，Eden区和From区已经被清空。**这个时候From和To会交换**，最后要保证To的Suvivor区为空，一直重复此过程直到To区被填满，To被填满后就会将对象移动到老年代中。

假设 如果存活100%，则复制会浪费大量时间，因此最好用在对象存活率低的时候使用

##### 标记清除

算法分为两个步骤，标记和清除，先标记出要回收的对象，然后统一回收这些对象，**节约空间，但是产生大量碎片**

##### 标记整理

算法分为，标记，压缩，清除三步骤。节约空间，没有碎片，但是花费时间最长。

##### 分代收集算法

 Stop the World

* 即Jvm由于要执行GC而停止应用程序的执行

* 任何一种GC算法中都会发生
* 多数GC优化通过减少 Stop the World发生的时间来提高程序性能

Safepoint

* 分析过程中对象引用关系不会变化的点
* 产生Safepoint的地方：方法调用，循环跳转，异常跳转
* 安全点数量得适中

**一般年轻代用复制算法，老年代用标记清除和标记整理结合**



#### 相关问题

##### Minor GC，Major GC，Full GC是回收哪里

Minor GC回收年轻代空间（包括 Eden 和 Survivor 区域），Major GC 是清理老年代，Full GC 是清理整个堆空间—包括年轻代和老年代，永久代。（注意 java8后，永久代已经被元空间替代了，并不会被gc回收了）

##### 触发Full GC条件

老年代空间不足

永久代空间不足（注意 java8后，永久代已经被元空间替代了，并不会被gc回收了）

CMS GC时出现promotion failed，concurrent mode failure；

Minor GC 晋升到老年代的平均大小大于老年代剩余空间

调用System.gc()

使用RMI进行RPC或管理JDK应用，每小时执行Full GC

# 谈谈你对GCRoots的理解

##### 刻意作为GC Root对象

1 虚拟机栈(栈帧中的本地变量表) 中引用的对象；

2 方法区中的类静态属性引用的对象

3 方法区中常量引用的对象；

4 本地方法栈中的JNI (一般说的Native方法)中引用的对象

5 活跃线程的引用对象

# 引用

##### 强引用

当内存不足，JVM开始垃圾回收，对于强引用的对象，就算出现OOM也不会对该对象进行回收

```java
public class jinfo {
    public static void main(String[] args) throws InterruptedException {
        Object o1=new Object();
        Object o2=o1;
        System.gc();
        System.out.println(o1);
        System.out.println(o2);

    }
}

```



##### 软引用

当系统内存充足 不会被回收， 当系统内存不充足 会被回收，一般用于高速缓存

```java
package JVM;

import java.lang.ref.SoftReference;

public class jinfo {
    public static void softRef_Memory_Enough() {
        Object o1=new Object();
        SoftReference<Object> softReference = new SoftReference<>(o1);
        System.out.println(o1);
        System.out.println(softReference.get());
        //内存够用，不会被回收
        o1=null;
        System.gc();

        System.out.println(o1);
        System.out.println(softReference.get());

    }

    public static void softRef_Memory_NotEnough() {

        Object o1=new Object();
        SoftReference<Object> softReference = new SoftReference<>(o1);
        System.out.println(o1);
        System.out.println(softReference.get());
        //内存不够用，会被回收
        o1=null;

        try {
            byte[] bytes = new byte[30 * 1024 * 1024];
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }finally {
            System.out.println(o1);
            System.out.println(softReference.get());

        }

    }
    public static void main(String[] args) throws InterruptedException {
//        softRef_Memory_Enough();
        softRef_Memory_NotEnough();
    }
}

```



#####  弱引用

只要垃圾回收机制一运行，不管内存多少  一定会回收

```java
package JVM;

import java.lang.ref.SoftReference;
import java.lang.ref.WeakReference;

public class jinfo {



    public static void main(String[] args) throws InterruptedException {
        Object o1 = new Object();
        WeakReference<Object> weakReference = new WeakReference<>(o1);
        System.out.println(o1);
        System.out.println(weakReference.get());

        o1=null;
        System.gc();


        System.out.println(o1);
        System.out.println(weakReference.get());
    }
}

```

##### 谈一下WeakHashMap

原来的HashMap  key为空也不会被gc回收

但是WeakHashMap，当key为空时，gc后，会回收这个map

```java
package JVM;
import java.lang.ref.SoftReference;
import java.lang.ref.WeakReference;
import java.util.WeakHashMap;
public class jinfo {
    public static void main(String[] args) throws InterruptedException {
        WeakHashMap<Integer, String> map = new WeakHashMap<>();
        Integer key = new Integer(2);
        String value = "WeakHashMap";

        map.put(key, value);
        System.out.println(map);

        key = null;
        System.out.println(map);

        System.gc();
        System.out.println(map);
    }
}

```

##### 虚引用

虚引用形同虚设，如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收

虚引用要配合引用队列来操作

```java
package JVM;

import java.lang.ref.PhantomReference;
import java.lang.ref.ReferenceQueue;
import java.lang.ref.SoftReference;
import java.lang.ref.WeakReference;
import java.util.WeakHashMap;

public class jinfo {

    public static void main(String[] args) throws InterruptedException {
        Object o1 = new Object();
        ReferenceQueue<Object> referenceQueue = new ReferenceQueue<>();
        //虚引用可以用 弱引用另一外一种调用方法代替
//        WeakReference<Object> weakReference = new WeakReference<>(o1, referenceQueue);
        //虚引用
        PhantomReference<Object> phantomReference = new PhantomReference<>(o1, referenceQueue);
        System.out.println(o1);
        System.out.println(phantomReference.get());
        System.out.println(referenceQueue.poll());

        o1=null;
        System.gc();
        Thread.sleep(500);
        System.out.println();

        System.out.println(o1);
        System.out.println(phantomReference.get());
        //调用gc后，虚引用进入 引用队列
        System.out.println(referenceQueue.poll());

    }
}

```



# 相关错误

##### java.lang.StackOverflowError

栈溢出，一般是递归过多

##### java.lang.OutOfMemoryError:java heap space

变量过多，堆满了

##### java.lang.OutOfMemoryError：GC overhead limit exceeded

gc回收时间过长抛出这个

##### java.lang.OutOfMemoryError：Direct buffer memory

写Nio的时候经常使用ByteBuffer读取和写入数据，这个基于通道与缓冲区的IO方式，它可以用Native函数库直接分配对外内存，然后通过一个存储在java堆里面的DirectByteBuffer对象作为这块内存的引用进行操作

避免了java堆和Native堆中来回复制数据



ByteBuffer.allocate(capability)  第一种方式是分配jvm内存，属于GC，由于需要拷贝，速度较慢

ByteBuffer.allocateDirect(capability)，第二种方式是分配OS本地内存，不属于GC管辖区域

由于不需要内存拷贝所以速度较快

由于不断分配本地内存，导致jvm内存很多空置，而本地内存却满了



先设置 -XX:MaxDirectMemorySize=5m  本地内存最多占用5m

```java
public class jinfo {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("目前最大内存"+sun.misc.VM.maxDirectMemory() / (double) 1024 / 1024);
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(6 * 1024 * 1024);
    }
}
```

 

##### java.lang.OutOfMemoryError：unable to create new native thread

一般出现在高并发情况下，例如 秒杀系统 同时1024个人下单就会报这个错误

导致原因是 1 创建应用线程太多了，超越系统承载极限

2 你的服务器并不允许你的应用程序创建这么多线程，linux系统默认允许单个进程可以创建线程数是1024

```java
public class jinfo {
    public static void main(String[] args) throws InterruptedException {
        for (int i = 1; ; i++) {
            System.out.println("*********i=" + i);
            new Thread(() -> {
                try {
                    Thread.sleep(Integer.MAX_VALUE);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }, "" + i).start();
        }
    }
}
```

解决方法

 1 降低线程数

2 提示linux承载最大数量



##### java.lang.OutOfMemoryError：Metaspace

动态生成很多的class，最终将这些class加载到 Metaspace 中。 随着生成的class越来越多, 最后将会占满 Metaspace 空间，随机爆出异常。



# 四种主要垃圾回收器

##### Serial串行垃圾回收器

只使用一个线程进行垃圾回收，会暂停所有用户线程，所以不适合服务器环境

对应JVM参数 -XX:+UseSerialGC

##### Parallel并行垃圾回收器

多个垃圾收集线程同时工作，会暂停所有用户线程，但是垃圾回收速度比串行快

##### CMS并发垃圾回收器(ConcMarkSweep)

用户线程和垃圾收集线程同时执行(不一定并行，有可能交替执行)，不需要停顿用户线程，一般用这个，使用对响应时间有要求的场景

##### G1 垃圾回收器

G1可以并发执行，整理空闲空间更快，需要更多的时间来预测GC停顿时间。不希望牺牲大量的吞吐性能，不用更大的java heap(**java9默认用这个**)

有以下特点

 1并行和并发

 2分代收集

 3空间整合

 4可预测的停顿

 5 将整个java堆内存划分成多个 大小相等的region

 6 年轻代和老年代不再物理隔离

###### G1比CMS的优势

G1是有整理内存过程的垃圾收集器，不会产生很多碎片。

G1的Stop The World(STW)更可控，G1在停顿时间增加了预测时间，用户可以指定期望停顿时间



查看默认垃圾回收器 java -XX:+PrintCommandLineFlags -version

###### 相关命令行

-XX:UseG1GC     使用G1垃圾回收器

-XX:G1HeapRegionSize=n 设置G1区域的大小，值为2的幂，范围1MB~32MB

-XX:MaxGCPauseMillis=n，最大GC停顿时间，JVM保证尽可能小于这个停顿时间

-XX:InitiatingHeapOccupancyPercent=n  堆占用多少就触发GC，默认45

-XX:ConcGCThreads=n 并发GC使用线程数



###### 与SpringBoot结合使用

1 IDEA开发完微服务工程

2 maven进行 clean  package 打包

3要求微服务启动的时候，同时配置JVM和GC调优参数

​	3.1 内

​	3.2 外 ===》重点

4 公式（+是打开  —是关闭）

​	Java -server  jvm的各种参数  -jar    第一部上面的jar/war包名字

​	例如 java -server -Xms1024m -Xmx1024m -XX:+UseG1GC -jar   xxxxxxx.war/jar



#  如何选择垃圾回收器



##### 单CPU或小内存，单机程序

-XX:+UseSerialGC

##### 多CPU，需要最大吞吐量。如后台计算机应用

-XX:+UseParallelGC 或者

-XX:+UseParallelOldGC 

##### 多CPU，追求低停顿时间，需要快速相应如互联网应用

-XX:+UseConcMarkSweepGC

-XX:+ParNewGC

| 参数                                      | 新生代垃圾回收器        | 新生代算法                   | 老年代垃圾收集器                                             | 老年代算法 |
| ----------------------------------------- | ----------------------- | ---------------------------- | ------------------------------------------------------------ | ---------- |
| -XX:+UseSerialGC                          | SerialGC                | 复制                         | SerialOldGC                                                  | 标整       |
| -XX:+UseParallelGC                        | ParNew                  | 复制                         | SerialOldGC                                                  | 标整       |
| -XX:+UseParallelOldGC /-XX:+UseParallelGC | Parallel                | 复制                         | Parallel Old                                                 | 标整       |
| -XX:+UseConcMarkSweepGC                   | ParNew                  | 复制                         | CMS+Serial Old的收集器组合(Serial Old作为CMS出错的后备收集器) | 标清       |
| -XX:+ParNewGC                             | G1整体采用标记-整理算法 | 局部是复制，不会产生内存碎片 |                                                              |            |



# 相关问题

##### Object的finalize()方法作用是否和c++析构函数作用相同

​	与C++析构函数不同，析构函数调用确定，而它的是不确定的

​	将未被引用的对象放置于F-Queue队列

​	方法执行随时可能被终止

​	给予对象最后一次重生的机会

