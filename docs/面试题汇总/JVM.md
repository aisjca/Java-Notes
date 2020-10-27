# 类加载器 

有三种类加载器

1. 引导类加载器：BootstrapClassLoader，启动类加载器负责加载存放在JDK\jre\lib(JDK代表JDK的安装目录，下同)下，或被-Xbootclasspath参数指定的路径中的类。
2. 扩展类加载器：ExtensionClassLoader，扩展类加载器负责加载JDK\jre\lib\ext目录中，或者由java.ext.dirs系统变量指定的路径中的所有类库（如javax.*开头的类）。
3. 应用类加载器：ApplicationClassLoader，应用类加载器负责加载用户类路径（ClassPath）所指定的类，开发者可以直接使用该类加载器。

# 类加载器的双亲委派机制

![双亲委派机制](/Users/jiangcheng/Java-Notes/图片/JVM图/双亲委派机制.jpeg)

双亲委派机制是当一个类收到类加载请求后，它首先不会自己尝试加载这个类，而是把这个请求交给父类，每一层加载类都是这样，会一直从自定义类加载器往上找，只有当父类加载器反馈自己无法完成的时候，子类加载器才会去加载。

例如：因为java自带String类，如果自己写了个父类的话，类加载器会找到java自带的String类，为了保证User编写的代码不污染java自带的代码，因此就有了双亲委派机制，保证了安全，确保使用不同的类加载器最终得到的都是同一个object对象。

## 为什么需要双亲委派 

防止多份同样字节码的加载

# Jvm类加载机制的流程

 Java中的类加载机制指虚拟机**把描述类的数据从 Class 文件加载到内存**，并对数据进行**校验、转换、解析和初始化**，最终形成可以被虚拟机直接使用的 Java 类型。

类从被加载到虚拟机内存中开始，到卸载出内存为止，它的整个生命周期包括了：**加载、验证、准备、解析、初始化、使用、卸载七个阶段**。类加载机制的保持则包括前面五个阶段。

1. 加载

   加载是指**将类的.class文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内，然后在堆区创建一个java.lang.Class对象，用来封装类在方法区内的数据结构。**

2. 验证

   验证的作用是确保被加载的类的正确性，包括文件格式验证，元数据验证，字节码验证以及符号引用验证。

3. 准备

   准备阶段为类的静态变量分配内存，并将其初始化为默认值。假设一个类变量的定义为public static int val = 3;那么变量val在准备阶段过后的初始值不是3而是0。

4. 解析

   解析阶段将类中**符号引用转换为直接引用**。符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能够无歧义的定位到目标即可。

5. 初始化

   初始化阶段**为类的静态变量赋予正确的初始值**，JVM负责对类进行初始化，主要对类变量进行初始化。

# java虚拟机的核心设计思想

# JVM内存如何分配

JVM中的内存主要划分为5个区域，即**方法区，堆内存，程序计数器，虚拟机栈以及本地方法栈**

![Jvm结构图](/Users/jiangcheng/Java-Notes/图片/JVM图/Jvm结构图.png)

1. 方法区：方法区是一个**线程之间共享**的区域。**常量，静态变量以及JIT编译后的代码都在方法区。主要用于存储已被虚拟机加载的类信息**，也可以称为“永久代”，垃圾回收效果一般，通过-XX：MaxPermSize控制上限。
2. 堆内存：堆内存是**垃圾回收**的主要场所，也是线程之间共享的区域，主要用来**存储创建的对象实例**，通过-Xmx 和-Xms 可以控制大小。
3. 虚拟机栈（栈内存）：**栈内存中主要保存**局部变量、基本数据类型变量**以及**堆内存中某个对象的引用变量**。每个方法在执行的同时都会创建一个栈帧（Stack Frame）用于存储局部变量表，操作数栈，动态链接，方法出口等信息。栈中的栈帧随着方法的进入和退出有条不紊的执行着出栈和入栈的操作。
4. 程序计数器：程序计数器是当前线程执行的**字节码的位置指示器**。字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令，是内存区域中唯一一个在虚拟机规范中**没有规定任何OutOfMemoryError情况**的区域。
5. 本地方法栈：主要是为JVM提供使用native 方法的服务。

#  堆区如何分类

VM的内存可以分为堆内存和非堆内存，堆内存分为**新生代和老年代**。年轻代又可以进一步划分为**一个Eden（伊甸）区和两个Survivor（幸存）区**组成。

![堆结构](/Users/jiangcheng/Java-Notes/图片/JVM图/堆结构.png)

## JVM堆内存的分配

JVM初始分配的堆内存由**-Xms**指定，默认是物理内存的1/64。JVM最大分配的堆内存由**-Xmx**指定，默认是物理内存的1/4。默认空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制。空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制。因此我们一般设置-Xms和-Xmx相等以避免在每次GC 后调整堆的大小。

通过参数**-Xmn2G** 可以设置年轻代大小为2G。通过**-XX:SurvivorRatio**可以设置年轻代中Eden区与Survivor区的比值，设置为8，则表示年轻代中Eden区与一块Survivor的比例为8：1。注意年轻代中有两块Survivor区域。

## JVM非堆内存的分配

JVM使用**-XX:PermSize** 设置非堆内存初始值，默认是物理内存的1/64。由**-XX:MaxPermSize**设置最大非堆内存的大小，默认是物理内存的1/4。



# 堆内存上对象的分配与回收

创建的对象会优先在Eden分配，如果是大对象（很长的字符串数组）则可以直接进入老年代。虚拟机提供一个**-XX:PretenureSizeThreshold参数**，令大于这个参数值的对象直接在老年代中分配，避免在Eden区和两个Survivor区发生大量的内存拷贝。

另外，**长期存活的对象将进入老年代**，每一次**MinorGC（年轻代GC）**，对象年龄就大一岁，默认15岁晋升到老年代，通过**-XX:MaxTenuringThreshold设置晋升年龄。**



# 堆与栈区别

管理方式：栈自动释放，堆要GC

空间大小：栈比堆小

碎片相关：栈产生的碎片远小于堆

分配方式：栈支持静态和动态分配，而堆是支持动态分配

效率：栈的效率比堆高

# 为什么要把字符串常量池放到堆区

# java8内存管理上的变化

java8永久代移除了，被元空间取代了，但是元空间本质和永久代差不多。

元空间和永久代最大区别是：永久代使用jvm堆内存，但是java8以后元空间不在虚拟机，而是使用本机的物理内存，元空间仅受本地内存大小限制

## 为什么要使用元空间代替永久代

字符串在永久代中，容易出现性能问题和内存溢出的问题。类和方法的信息等比较难确定大小，因此对于永久代的大小指定比较困难，太小容易出现永久代溢出。使用元空间则使用了本地内存。

# 判断对象已经无效

 判断一个对象是否应该被回收，主要是看其是否还有引用。判断对象是否存在引用关系的方法包括引用计数法以及root根搜索方法。

## 引用计数法

是一种比较古老的回收算法。原理是此对象有一个引用，即增加一个计数，删除一个引用则减少一个计数。垃圾回收时，只需要收集计数为0的对象。此算法最致命的是无法处理循环引用的问题，如A引用B，B引用A，双方计数都是1，导致无法回收。

## root根搜索方法

root搜索方法的基本思路就是通过一系列可以做为root的对象作为起始点，从这些节点开始向下搜索。当一个对象到root节点没有任何引用链接时，则证明此对象是可以被回收的。以下对象会被认为是root对象：

1. 栈内存中引用的对象
2. 方法区中静态引用和常量引用指向的对象
3. 被启动类（bootstrap加载器）加载的类和创建的对象
4. Native方法中JNI引用的对象。

# 对象的引用

如果reference类型的数据中存储的数值代表的是另外一块内存的起始地址，就称为这块内存代表一个引用。JDK1.2以后将引用分为强引用，软引用，弱引用和虚引用四种。

1. **强引用：**普通存在， P p = new P()，只要强引用存在，垃圾收集器永远不会回收掉被引用的对象。
2. **软引用：**通过SoftReference类来实现软引用，在内存不足的时候会将这些软引用回收掉。
3. **弱引用：**通过WeakReference类来实现弱引用，每次垃圾回收的时候肯定会回收掉弱引用。
4. **虚引用：**也称为幽灵引用或者幻影引用，通过PhantomReference类实现。设置虚引用只是为了对象被回收时候收到一个系统通知。

#  引用计数法实现原理

#  哪些对象可以作为GC Root

#  GCRoot详细

#  finalize()方法回收对象的两次标记过程

#  Java四种引用及场景 

### 强引用

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



### 软引用

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



###  弱引用

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

### 虚引用

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



#  垃圾回收算法和流程

#  如何减少full gc的次数？ 

#  方法区是否需要gc

#  查看GC状态的命令

#  CMS收集器的流程 

#  G1收集器的流程

#  CMS收集器和G1收集器的区别

#  内存管理(对象创建、布局、访问定位)

# 查看java虚拟机内存占用？

# 线程上下文类加载器 

# 对象的创建过程的内存分配

一般情况下我们通过new指令来创建对象，当虚拟机遇到一条new指令的时候，会去检查这个指令的参数是否能在常量池中定位到某个类的符号引用，并且检查这个符号引用代表的类是否已经被加载，解析和初始化。如果没有，那么会执行类加载过程。通过执行类的加载，验证，准备，解析，初始化步骤，完成了类的加载，这个时候会为该对象进行内存分配，也就是把一块确定大小的内存从Java堆中划分出来，在分配的内存上完成对象的创建工作。

## 内存分配方式

1. 指针碰撞

   假设Java堆中的内存是绝对规整的，用过的内存在一边，未使用的内存在另一边，中间有一个指示指针，那么所有的内存分配就是把那个指针向空闲空间那边挪动一段与对象大小相等的距离。

2. 空闲列表方式

   如果Java堆内存中不是规整的，已使用和未使用的内存相互交错，那么虚拟机就必须维护一个列表用来记录哪块内存是可用的，在分配的时候找到一块足够大的空间分配对象实例，并且需要更新列表上的记录。需要注意的是，Java 堆内存是否规整是由所使用的垃圾收集器是否拥有压缩整理功能来决定的。

## 内存分配的线程安全问题

1. 对分配内存空间的动作进行同步处理，通过CAS + 失败重试的方式保证更新指针操作的原子性
2. 把分配内存的动作按照线程划分在不同的空间之中，即给每一个线程都预先分配一小段的内存，称为**本地线程分配缓存（TLAB）**，只有TLAB用完并分配新的TLAB时，才需要进行同步锁定。 虚拟机是否使用TLAB，可以通过**-XX: +/-UserTLAB**参数来设定



# 对象被访问的时候是怎么被找到的

当创建一个对象的时候，在栈内存中会有一个引用变量，指向堆内存中的某个具体的对象实例。Java虚拟机规范中并**没有规定这个引用变量应该以何种方式去定位和访问堆内存中的具体对象**。目前常见的对象访问方式有两种，即句柄访问方式和直接指针访问方式，分别介绍如下。

1. 句柄访问方式：在JVM的堆内存中划分出一块内存来作为句柄池，引用变量中存储的就是对象的句柄地址，句柄中包含了对象实例数据与类型数据各自的具体地址信息。在内存垃圾收集之后，对象会移动，但是引用reference中存储的是稳定的句柄地址，但是句柄地址方式不直接，访问速度较慢。
2. 直接指针访问方式：引用变量中存储的就是对象的直接地址，通过指针直接访问对象。直接指针的访问方式节省了一次指针定位的时间开销，速度较快。Sun HotSpot使用了直接指针方式进行对象的访问。

# Class类文件怎么回收 

# 堆内存上的对象回收也叫做垃圾回收，那么垃圾回收什么时候开始

垃圾回收主要是完成**清理对象，整理内存**的工作。GC经常发生的区域是堆区，堆区还可以细分为新生代、老年代。新生代还分为一个Eden区和两个Survivor区。垃圾回收分为年轻代区域发生的Minor GC和老年代区域发生的Full GC。

## Minor Gc

对象优先在Eden中分配，当Eden中没有足够空间时，虚拟机将发生一次Minor GC，因为Java大多数对象都是朝生夕灭，所以Minor GC非常频繁，而且速度也很快。

## FULL Gc

Full GC是指发生在老年代的GC，当老年代没有足够的空间时即发生Full GC，发生Full GC一般都会有一次Minor GC。

# 内存分配分配和回收

## 动态对象年龄判定

如果Survivor空间中相同年龄所有对象的大小总和大于Survivor空间的一半，那么**年龄大于等于该对象年龄的对象即可晋升**到老年代，不必要等到-XX:MaxTenuringThreshold。

## 空间分配担保：

发生Minor GC时，虚拟机会检测之前每次晋升到老年代的平均大小是否大于老年代的剩余空间大小。如果大于，则进行一次**Full GC（老年代GC）**，如果小于，则查看**HandlePromotionFailure设置是否允许担保失败**，如果允许，那只会进行一次**Minor GC**，如果不允许，则改为进行一次**Full GC**。