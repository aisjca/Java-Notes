

# 线程与进程区别

## 主要区别

1. 进程是一个“执行中的程序”，是系统进行**资源分配和调度**的一个独立单位
2. 线程是进程的一个实体，一个进程中一般拥有多个线程。线程之间**共享地址空间**和其它资源（所以通信和同步等操作,线程比进程更加容易）
3. 线程一般不拥有系统资源，但是也有一些必不可少的资源（使用ThreadLocal存储）
4. 线程上下文的切换比进程上下文切换要快很多。

## 线程上下文切换比进程上下文切换快的原因

1. 进程切换时：涉及到当前进程的CPU环境的保存和新被调度运行进程的CPU环境的设置
2. 线程切换时，仅需要保存和设置少量的寄存器内容，不涉及存储管理方面的操作

# 线程通信同步有几种方式

## 线程通信

1. 使用全局变量：主要由于多个线程可能更改全局变量，因此全局变量最好声明为volatile。
2. 使用消息实现通信：每一个线程都可以拥有自己的消息队列（UI线程默认自带消息队列和消息循环，工作线程需要手动实现消息循环），因此可以采用消息进行线程间通信sendMessage,postMessage。
3. 使用事件CEvent类实现线程间通信：Event对象有两种状态分别是有信号和无信号，线程可以监视处于有信号状态的事件，以便在适当的时候执行对事件的操作。

## 线程同步方式

1. 临界区

   保证在某一时刻只有一个线程能访问数据的简便办法。在任意时刻只允许一个线程对共享资源进行访问。如果有多个线程试图同时访问临界区，那么 在有一个线程进入后其他所有试图访问此临界区的线程将被挂起，并一直持续到进入临界区的线程离开。临界区在被释放后，其他线程可以继续抢占，并以此达到用原子方式操 作共享资源的目的。 仅能在同一进程内使用

2. 互斥量

   只有拥有互斥对象的线程才具有访问资源的权限，由于互斥对象只有一个，因此就决定了任何情况下此共享资源都不会同时被多个线程所访问。当前占据资源的线程在任务处理完后应将拥有的互斥对象交出，以便其他线程在获得后得以访问资源。互斥量比临界区复杂。因为使用互斥不仅仅能够在同一应用程序不同线程中实现资源的安全共享，而且可以在不同应用程序的线程之间实现对资源的安全共享。 

3. 信号量

   信号允许多个线程同时使用共享资源 ，这与操作系统中的PV操作相同。

4. 事件

   事件机制，则允许一个线程在处理完一个任务后，主动唤醒另外一个线程执行任务。

## 线程之间制约关系

1. 直接制约关系：即一个线程的处理结果，为另一个线程的输入，因此线程之间直接制约着，这种关系可以称之为同步关系
2. 间接制约关系：即两个线程需要访问同一资源，该资源在同一时刻只能被一个线程访问，这种关系称之为线程间对资源的互斥访问，某种意义上说互斥是一种制约关系更小的同步

# 进程通信同步有几种方式

1. 通过使用套接字Socket来实现不同机器间的进程通信
2. 通过映射一段可以被多个进程访问的共享内存来进行通信
3. 通过写进程和读进程利用管道进行通信

# 多线程共享数据

1. 每个线程执行流程相同，使用同一个Runnable对象，Runnable对象中有共享数据

   ```java
   public class Main {
   
       public static void main(String[] args) {
           Ticket ticket = new Ticket();
           new Thread(ticket).start();
           new Thread(ticket).start();
       }
   
   
   }
   class Ticket implements Runnable {
       private int ticket = 10;
   
       @Override
       public synchronized void run() {
           while (ticket > 0) {
               ticket--;
               System.out.println("当前线程窗口" + Thread.currentThread().getName() + "剩余票为: " + ticket);
           }
       }
   }
   
   /**
   当前线程窗口Thread-0剩余票为: 9
   当前线程窗口Thread-0剩余票为: 8
   当前线程窗口Thread-0剩余票为: 7
   当前线程窗口Thread-0剩余票为: 6
   当前线程窗口Thread-0剩余票为: 5
   当前线程窗口Thread-0剩余票为: 4
   当前线程窗口Thread-0剩余票为: 3
   当前线程窗口Thread-0剩余票为: 2
   当前线程窗口Thread-0剩余票为: 1
   当前线程窗口Thread-0剩余票为: 0
   /
   ```

   

2. 每个线程执行流程不相同，使用不同的Runnable对象

   ```java
   public class Main {
   
       public static void main(String[] args) {
           ShareData data = new ShareData();
           new Thread(new Runnable1(data)).start();
           new Thread(new Runnable2(data)).start();
       }
   }
   
   class Runnable1 implements Runnable {
   
       private ShareData data;
   
       public Runnable1(ShareData data) {
           this.data = data;
       }
   
       @Override
       public void run() {
               data.increment();
       }
   }
   
   class Runnable2 implements Runnable {
   
       private ShareData data;
   
       public Runnable2(ShareData data) {
           this.data = data;
       }
   
       @Override
       public void run() {
               data.decrement();
       }
   }
   class ShareData {
   
       private int j = 10;
   
       public synchronized void increment() {
           j++;
           System.out.println("线程：" + Thread.currentThread().getName() + "加操作之后，j = " + j);
       }
   
       public synchronized void decrement() {
           j--;
           System.out.println("线程：" + Thread.currentThread().getName() + "减操作之后，j = " + j);
       }
   }
   
   /**
   线程：Thread-0加操作之后，j = 11
   线程：Thread-1减操作之后，j = 10
   /
   ```

# 信号量和互斥量的区别  

主要区别

1. 互斥量用于线程的互斥，信号量用于线程的同步
2. 互斥是指某一资源同时只允许一个访问者对其进行访问，具有唯一性和排它性。但互斥无法限制访问者对资源的访问顺序，即访问是无序的。同步是指在互斥的基础上（大多数情况），通过其它机制实现访问者对资源的有序访问。在大多数情况下，同步已经实现了互斥，特别是所有写入资源的情况必定是互斥的。少数情况是指可以允许多个访问者同时访问资源。
3. 互斥量值只能为0/1，信号量值可以为非负整数。
4. 一个互斥量只能用于一个资源的互斥访问，它不能实现多个资源的多线程互斥问题。信号量可以实现多个同类资源的多线程互斥和同步。当信号量为单值信号量是，也可以完成一个资源的互斥访问。
5. 互斥量的加锁和解锁必须由同一线程分别对应使用，信号量可以由一个线程释放，另一个线程得到。

# 线程是怎么实现的

主要有三种方式实现线程

## 在用户空间中实现线程

### 结构图

![用户空间实现线程](/Users/jiangcheng/Java-Notes/图片/Java并发/用户空间实现线程.png)

### 原理

把整个线程包放在用户空间，内核对线程包一无所知。用户调用库函数实现线程，，在用户空间管理线程时，每个进程有个专用的线程表，用来跟踪进程中的线程，这些表和内核中的进程类似，它记录着各个线程属性，每个线程的程序计算器、堆栈、指针、寄存器和状态等。

### 优点

1. 用户线程包可以在不支持线程的操作系统上实现，只需要调用函数库实现线程即可。
2. 由于线程表在本地，启动线程比进行内核调用效率更高，并且不需要陷入内核，不需要本地上下文切换，也不用对内存进行高速缓存进行刷新。
3. 允许每个进程都有自己的调度算法。

### 缺点

1. 无法实现阻塞系统调用
2. 会发生缺页中断问题，如果有一个线程引起页面故障，由于线程不在内核，内核不知道线程的存在，内核会把整个进程进行阻塞，直到磁盘IO完成为止，导致其他可以运行的线程也被阻塞。
3. 一旦一个线程开始运行，那么该进程中的其他线程不能运行，除非第一个线程自动放弃CPU。

## 在内核中实现线程

### 结构图

![内核中实现线程](/Users/jiangcheng/Java-Notes/图片/Java并发/内核中实现线程.png)

### 原理

每个进程中没有线程表，线程表在哪喝中。当某个线程希望创建一个新线程或者撤销一个已有线程的时候，它会进行一个系统调用，这个系统调用会通过对线程表的更新完成线程创建或者撤销工作。

### 优点

1. 内核线程不需要任何新的，非阻塞系统的调用。
2. 可以回收线程(当某个线程被撤销，可以标志它为不可运行)

### 缺点

1. 一个多线程创建新的进程时，难以确定新进程时拥有与原进程相同数量的线程，或者是只有一个线程。
2. 当进程接收到一个信号时候，难以确定用哪一个线程处理它。

## 混合实现

### 结构图

![线程混合实现](/Users/jiangcheng/Java-Notes/图片/Java并发/线程混合实现.png)

### 原理

内核只识别内核级别线程，并对其进行调度。一些内核级线程会被多个用户线程多路复用。内线线程可以创建，撤销，调度这些用户线程，每个内核级线程有一个可以轮流使用的用户级线程集合。

### 优点

1. 结合了内核实现线程和用户空间实现线程的优点。
2. 多路复，每个内核级线程有一个可以轮流使用的用户级线程集合。

# 线程死锁

死锁是最常见的一种线程活性故障。死锁的起因是多个线程之间相互等待对方而被永远暂停（处于非Runnable）。死锁的产生必须满足如下四个必要条件：

1. 资源互斥：一个资源每次只能被一个线程使用
2. 请求和保持：：一个线程因请求资源而阻塞时，对已获得的资源保持不放
3. 不可剥夺：线程已经获得的资源，在未使用完之前，不能强行剥夺
4. 循环等待：若干线程之间形成一种头尾相接的循环等待资源关系

# 如何避免死锁

1. 粗锁法：使用一个粒度粗的锁来消除“请求与保持条件”，缺点是会明显降低程序的并发性能并且会导致资源的浪费。
2. 锁排序法：比如某个线程只有获得A锁和B锁，通过指定锁的获取顺序，比如规定，只有获得A锁的线程才有资格获取B锁，按顺序获取锁就可以避免死锁。这通常被认为是解决死锁很好的一种方法。
3. 使用显式锁中的**ReentrantLock.try(long,TimeUnit)**来申请锁

# wait和sleep的区别  

1. wait：是Object的方法，必须与synchronized关键字一起使用，线程进入阻塞状态，当notify或者notifyall被调用后，会解除阻塞。但是，只有**重新占用互斥锁**之后才会进入可运行状态。睡眠时，会释放互斥锁。
2. sleep：是Thread类的静态方法，当前线程将睡眠n毫秒，线程进入阻塞状态。当睡眠时间到了，会解除阻塞，进入可运行状态，等待CPU的到来。睡眠不释放锁（如果有的话）。

# 用start()方法去执行run()方法而不是直接调用run()方法

## start方法

用  start方法来启动线程，是真正实现了多线程，  通过调用Thread类的start()方法来启动一个线程，这时此线程处于就绪（可运行）状态，并没有运行，一旦得到cpu时间片，就开始执行run()方法。但要注意的是，此时无需等待run()方法执行完毕，即可继续执行下面的代码。所以run()方法并没有实现多线程。

## run方法

 run()方法只是类的一个普通方法而已，如果直接调用Run方法，程序中依然只有主线程这一个线程，其程序执行路径还是只有一条，还是要顺序执行，还是要等待run方法体执行完毕后才可继续执行下面的代码。

# 上下文切换的种类

| 上下文切换的种类 |                                        |
| ---------------- | -------------------------------------- |
| 线程切换         | 同一进程中的两个线程之间的切换         |
| 进程切换         | 两个进程之间的切换                     |
| 模式切换         | 在给定线程中，用户模式和内核模式的切换 |
| 地址空间切换     | 将虚拟内存切换到物理内存               |

# 线程有几种状态和它的上下文切换

## Java线程的6种状态

线程有六种状态：

1. NEW：线程创建，但没有启动
2. RUNNABLE：代表线程正在运行或者不处于阻塞、等待状态的可以被运行的状态。线程创建后或脱离阻塞、等待状态后进入可运行状态。
3. BLOCKED：代表线程尝试获得一个锁时无法对锁占用，进入阻塞状态；当该线程能够获得锁时脱离阻塞状态。
4. WAITING：等待线程主动进入等待条件达成的状态，可以使用join、wait、sleep方法。
5. TIMED_WAITING：等待状态添加计时器，当等待条件达成或计时器耗尽时脱离等待状态。
6. TERMINATED：线程任务结束或手动设置中断标记。

## 操作系统的5种状态

1. 新建状态(New)：线程创建但没有启动
2. 就绪状态(Runnable)：线程对象创建后，其他线程(比如 main 线程）调用了该对象 的 start ()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取cpu的使用权 。
3. 运行状态(Running)：就绪状态( runnable )的线程获得了cpu时间片(timeslice )，执行程序代码。
4. 阻塞状态(Blocked)：线程放弃CPU的时间片（一直到某个条件达成），主动进入阻塞的状态，有三种阻塞状态，分别是
   1. 同步阻塞：线程由于尝试获得对象的同步锁但无法取得时，进入锁池，等待其他线程释放该对象的锁。  
   2. 等待阻塞：线程主动放弃对对象上的锁的占用，进入等待对象通知的队列。指wait方法  
   3. 其他阻塞：线程主动进入休眠状态，等待条件达成。指sleep、join方法或I/O请求。
5. 线程死亡(Dead)：线程 run ()、 main () 方法执行结束，或者因异常退出了 run ()方法，则该线程结束生命周期。死亡的线程不可再次复生。

## 线程运行状态图

![线程运行状态图](/Users/jiangcheng/Java-Notes/图片/Java并发/线程运行状态图.png)

## 线程上下文切换

### 概念

上下文切换是值CPU通过**分配时间片**来执行任务，当一个任务的时间片用完，就会切换到另一个任务。在切换之前会**保存上一个任务的状态**，当下次再切换到该任务，就会加载这个状态。(**任务从保存到再加载的过程就是一次上下文切换**)。

### 线程切出

一个线程被剥夺处理器的使用权而被暂停运行，操作系统会将线程的进度信息保存到内存。

### 线程切入

一个线程被系统选中占用处理器开始或继续运行，操作系统需要从内存中加载线程的上下文。

### 上下文切换的种类

#### 自发性上下文切换

线程由自身因素导致的上下文切换

1. Thread.sleep()
2. Object.wait()
3. Thread.yeild()
4. Thread.join()
5. LockSupport.park()

#### 非自发性上下文切换

线程由于线程调度器的原因被迫切出

1. 线程的时间片用完,导致线程切出。
2. 有一个比线程优先级更高的线程需要被运行，导致线程切出。
3. 虚拟机的垃圾回收动作。

### 上下文切换开销

#### 简接开销

1. 处理器高速缓存重新加载内存时的开销。
2. 可能导致整个一级高速缓存中的内容被冲刷，即被写入到下一级高速缓存或主存。

#### 直接开销

1. 操作系统保存回复上下文所需的开销
2. 线程调度器调度线程的开销

# ThreadLocal是什么

使用ThreadLocal维护变量时，其为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立的改变自己的副本，而不会影响其他线程对应的副本。 

## 内部实现机制

1. 每个线程内部都会维护一个类似HashMap的对象，称为**ThreadLocalMap**，里边会包含若干了**Entry（K-V键值对）**，相应的线程被称为这些Entry的属主线程
2. **Entry的Key是一个ThreadLocal实例，Value是一个线程特有对象**。Entry的作用是为其属主线程建立起一个ThreadLocal实例与一个线程特有对象之间的对应关系
3. Entry对Key的引用是弱引用；Entry对Value的引用是强引用。

# 就绪状态和阻塞状态有什么区别

## 就绪状态

当线程对象创建后，其他线程调用它的start方法，其进入线程等待池，等待cpu使用权

## 阻塞状态

线程放弃cpu的使用权，进入阻塞状态直到重新进入就绪状态，阻塞分为三种：

1. 同步阻塞：线程由于尝试获得对象的同步锁但无法取得时，进入锁池，等待其他线程释放该对象的锁。  
2. 等待阻塞：线程主动放弃对对象上的锁的占用，进入等待对象通知的队列。指wait方法  
3. 其他阻塞：线程主动进入休眠状态，等待条件达成。指sleep、join方法或I/O请求。

## 两者可以互相切换吗 

阻塞状态可以通过获得锁、sleep()方法结束、调用join()的线程运行结束方法后可以切换到就绪状态，但是就绪状态要先获取cpu时间片变成运行状态后，才能转换到阻塞状态。

# 进程和线程切换开销对比 

## 虚拟内存

虚拟内存是操作系统为每个进程提供的一种抽象，每个进程都有属于自己的、私有的、地址连续的虚拟内存，当然我们知道最终进程的数据及代码必然要放到物理内存上，那么必须有某种机制能记住虚拟地址空间中的某个数据被放到了哪个物理内存地址上，这就是所谓的地址空间映射，操作系统是通关页表来记住这种映射关系。**每个进程都有自己的虚拟地址空间，进程内的所有线程共享进程的虚拟地址空间**。

## 进程切换和线程切换的对比

* 最主要区别是进程切换涉及虚拟地址空间的切换而线程不涉及。由于每个进程都有自己的虚拟地址空间，而线程是共享虚拟地址空间。因此进程切换的时候涉及虚拟地址空间的切换，而同一个进程的线程切换不涉及虚拟地址空间的切换。

### 为什么虚拟地址空间切换会比较耗时

每个进程都有自己的虚拟地址空间，把虚拟地址转换为物理地址需要查找页表，页表查找是一个很慢的过程，因此通常使用Cache来缓存常用的地址映射，这样可以加速页表查找，这个cache就TLB（translation Lookaside Buffer，TLB本质上就是一个cache，是用来加速页表查找的）。由于每个进程都有自己的虚拟地址空间，那么显然每个进程都有自己的页表，那么**当进程切换后页表也要进行切换，页表切换后TLB就失效了**，cache失效导致命中率降低，那么虚拟地址转换为物理地址就会变慢，表现出来的就是程序运行会变慢，而线程切换则不会导致TLB失效，因为线程线程无需切换地址空间，因此我们通常说线程切换要比较进程切换块，原因就在这里。

# Synchronized怎么实现线程同步

synchronized是悲观锁，在操作同步资源之前需要给同步资源先加锁，这把锁就是存在Java对象头里，每一个被锁住的对象都会和一个monitor有关联。

## Java对象头

以Hotspot虚拟机为例，Hotspot的对象头主要包括两部分数据：Mark Word（标记字段）、Klass Pointer（类型指针）。

#### Mark Word

默认存储**对象的HashCode，分代年龄和锁标志位信息**。这些信息都是与对象自身定义无关的数据，所以Mark Word被设计成一个非固定的数据结构以便在极小的空间内存存储尽量多的数据。它会根据对象的状态复用自己的存储空间，也就是说在运行期间Mark Word里存储的数据会随着锁标志位的变化而变化。

### Klass Point

对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。

## Monitor

Monitor可以理解为一个同步工具或一种同步机制，通常被描述为一个对象。每一个Java对象就有一把看不见的锁，称为内部锁或者Monitor锁。

Monitor是线程私有的数据结构，每一个线程都有一个可用monitor record列表，同时还有一个全局的可用列表。每一个被锁住的对象都会和一个monitor关联，同时monitor中有一个Owner字段存放拥有该锁的线程的唯一标识，表示该锁被这个线程占用。

## 为什么JDK 6之前synchronized效率低的

因为最初synchronized最初实现同步的方式是若有多个线程想获取同步资源，synchronized会阻塞其他资源，只有一个线程能获得锁，等这个线程执行完再唤醒其他线程。

* 但是阻塞或唤醒一个Java线程需要操作系统切换CPU状态来完成，这种状态转换需要耗费处理器时间。如果同步代码块中的内容过于简单，状态转换消耗的时间有可能比用户代码执行的时间还要长。

#### 改进方式

JDK 6中为了减少获得锁和释放锁带来的性能消耗，引入了“偏向锁”和“轻量级锁”。共有4种锁的状态

| 锁状态   | 存储内容                                                | 存储内容 |
| :------- | :------------------------------------------------------ | :------- |
| 无锁     | 对象的hashCode、对象分代年龄、是否是偏向锁（0）         | 01       |
| 偏向锁   | 偏向线程ID、偏向时间戳、对象分代年龄、是否是偏向锁（1） | 01       |
| 轻量级锁 | 指向栈中锁记录的指针                                    | 00       |
| 重量级锁 | 指向互斥量（重量级锁）的指针                            | 10       |



# Java中的锁

## 锁的总结图

![Java锁总结](/Users/jiangcheng/Java-Notes/图片/Java并发/Java锁总结.png)

## 乐观锁和悲观锁

### 乐观锁

乐观锁认为自己在使用数据时不会有别的线程修改数据，所以不会添加锁，只是在更新数据的时候去判断之前有没有别的线程更新了这个数据。如果这个数据没有被更新，当前线程将自己修改的数据成功写入。如果数据已经被其他线程更新，则根据不同的实现方式执行不同的操作（例如报错或者自动重试）。

#### 例子

![乐观锁](/Users/jiangcheng/Java-Notes/图片/Java并发/乐观锁.png)

### 悲观锁

对于同一个数据的并发操作，悲观锁认为自己在使用数据的时候一定有别的线程来修改数据，因此在获取数据的时候会先加锁，确保数据不会被别的线程修改。Java中，synchronized关键字和Lock的实现类都是悲观锁。

#### 例子

![悲观锁](/Users/jiangcheng/Java-Notes/图片/Java并发/悲观锁.png)

### 适用场景

* 乐观锁适合读操作多的场景，不加锁可以大幅提高读操作的效率。
* 悲观锁适合写操作多的场景，先加锁，保证操作数据的正确性。

## 非公平锁和公平锁

### 公平锁

公平锁是指多个线程按照申请锁的顺序来获取锁，线程直接进入队列中排队，队列中的第一个线程才能获得锁，其他线程按申请锁的顺序排队。

#### 优点

等待锁的线程不会饿死。

#### 缺点

整体吞吐效率相对非公平锁要低，等待队列中除第一个线程以外的所有线程都会阻塞，CPU唤醒阻塞线程的开销比非公平锁大。

#### 例子

![公平锁](/Users/jiangcheng/Java-Notes/图片/Java并发/公平锁.png)

如上图所示，假设学生排队打饭，饭堂阿姨有一把锁，只有拿到锁的人才能够打饭，打完饭要把锁还给饭堂阿姨。每个过来打饭的人都要饭堂阿姨的允许并拿到锁之后才能去打饭，如果前面有人正在打饭，那么这个想要打饭的学生就必须排队。饭堂阿姨会查看下一个要去打饭的人是不是队伍里排最前面的人，如果是的话，才会给你锁让你去打饭；如果你不是排第一的人，就必须去队尾排队，这就是公平锁。

#### 源码

ReentrantLock类里面的tryAcquire方法

```java
protected final boolean tryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (!hasQueuedPredecessors() &&
            compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```

### 非公平锁

非公平锁是多个线程加锁时直接尝试获取锁，获取不到才会到等待队列的队尾等待。但如果此时锁刚好可用，那么这个线程可以无需阻塞直接获取到锁，所以非公平锁有可能出现**后申请锁**的线程先获取锁的场景。

#### 优点

是可以减少唤起线程的开销，整体的吞吐效率高，因为线程有几率不阻塞直接获得锁，CPU不必唤醒所有线程。

#### 缺点

处于等待队列中的线程可能会饿死，或者等很久才会获得锁。

#### 例子

![非公平锁](/Users/jiangcheng/Java-Notes/图片/Java并发/非公平锁.png)

对于非公平锁，就相当于来了一个坏学生，坏学生想要插队，如果在上一个人刚打完饭把锁还给饭堂阿姨而且饭堂阿姨还没有允许等待队伍里下一个人去打饭时，刚好来了一个插队的人，这个插队的人是可以直接从饭堂阿姨那里拿到锁去打饭，不需要排队，原本排队等待的人只能继续等待。

#### 源码

ReentrantLock类里面的nonfairTryAcquire方法

```java
final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```

### 两者源码区别

公平锁与非公平锁的lock()方法唯一的区别就在于公平锁在获取同步状态时多了一个限制条件：hasQueuedPredecessors()。hasQueuedPredecessors()源码如下

```java
public final boolean hasQueuedPredecessors() {
    // The correctness of this depends on head being initialized
    // before tail and on head.next being accurate if the current
    // thread is first in queue.
    Node t = tail; // Read fields in reverse initialization order
    Node h = head;
    Node s;
    return h != t &&
        ((s = h.next) == null || s.thread != Thread.currentThread());
}
```

该方法主要做一件事情：就是判断当前线程是否位于同步队列中的第一个。如果是则返回true，否则返回false。

## 独享锁和共享锁

ReentrantReadWriteLock的部分源码,可以看到里面有ReadLock和WriteLock这两把锁，ReadLock是共享锁，WriteLock是独享锁。

```java
public class ReentrantReadWriteLock
        implements ReadWriteLock, java.io.Serializable {
    private static final long serialVersionUID = -6992448646407690164L;
    /** Inner class providing readlock */
    private final ReentrantReadWriteLock.ReadLock readerLock;
    /** Inner class providing writelock */
    private final ReentrantReadWriteLock.WriteLock writerLock;
    /** Performs all synchronization mechanics */
    final Sync sync;
}
```

### 独享锁

独享锁也叫排他锁，是指该锁一次只能被一个线程所持有。如果线程T对数据A加上排它锁后，则其他线程不能再对A加任何类型的锁。获得排它锁的线程即能读数据又能修改数据。JDK中的synchronized和JUC中Lock的实现类就是互斥锁。

#### 源码

```java
public static class WriteLock implements Lock, java.io.Serializable {
    private static final long serialVersionUID = -4992448646407690164L;
    private final Sync sync;

   
    protected WriteLock(ReentrantReadWriteLock lock) {
        sync = lock.sync;
    }
```

### 共享锁

共享锁是指该锁可被多个线程所持有。如果线程T对数据A加上共享锁后，则其他线程只能对A再加共享锁，不能加排它锁。获得共享锁的线程只能读数据，不能修改数据。

#### 源码

```java
public static class ReadLock implements Lock, java.io.Serializable {
        private static final long serialVersionUID = -5992448646407690164L;
        private final Sync sync;

        protected ReadLock(ReentrantReadWriteLock lock) {
            sync = lock.sync;
        }
}
```

### 读锁和写锁的加锁方式区别

首先利用state字段存储读锁和写锁。在独享锁中这个值通常是**0或者1**（如果是重入锁的话state值就是重入的次数），在共享锁中state就是**持有锁的数量**。由于在ReentrantReadWriteLock中有读、写两把锁，所以需要在一个整型变量state(int 32位)上分别描述读锁和写锁的数量。于是将state变量按位切割切分成了两个部分，高16位表示读锁的状态（读锁个数），低16位表示写锁的状态（写锁个数）。

![state状态图](/Users/jiangcheng/Java-Notes/图片/Java并发/state状态图.png)

#### 读锁源码

```java
protected final int tryAcquireShared(int unused) {
    Thread current = Thread.currentThread();
    int c = getState();
    if (exclusiveCount(c) != 0 &&
        getExclusiveOwnerThread() != current)
        return -1;
    int r = sharedCount(c);
    if (!readerShouldBlock() &&
        r < MAX_COUNT &&
        compareAndSetState(c, c + SHARED_UNIT)) {
        if (r == 0) {
            firstReader = current;
            firstReaderHoldCount = 1;
        } else if (firstReader == current) {
            firstReaderHoldCount++;
        } else {
            HoldCounter rh = cachedHoldCounter;
            if (rh == null || rh.tid != getThreadId(current))
                cachedHoldCounter = rh = readHolds.get();
            else if (rh.count == 0)
                readHolds.set(rh);
            rh.count++;
        }
        return 1;
    }
    return fullTryAcquireShared(current);
}
```

执行步骤

1. 如果其他线程已经获取了写锁，则当前线程获取读锁失败，进入等待状态。
2. 如果当前线程获取了写锁或者写锁未被获取，则当前线程增加读状态，成功获取读锁。读锁的每次释放（线程安全的，可能有多个读线程同时释放读锁）均减少读状态，减少的值是“1<<16”。

#### 写锁源码

```java
static final int SHARED_SHIFT   = 16;
static final int EXCLUSIVE_MASK = (1 << SHARED_SHIFT) - 1;
static final int MAX_COUNT      = (1 << SHARED_SHIFT) - 1;
static int exclusiveCount(int c) { return c & EXCLUSIVE_MASK; }

protected final boolean tryAcquire(int acquires) {
    Thread current = Thread.currentThread();
    int c = getState();//获取锁的个数
    int w = exclusiveCount(c);//把写锁的数目
    if (c != 0) {
        // (Note: if c != 0 and w == 0 then shared count != 0)
        if (w == 0 || current != getExclusiveOwnerThread())//若写锁数量为0或持有锁的线程不是当前线程就返回失败。
            return false;
        if (w + exclusiveCount(acquires) > MAX_COUNT)//如果写入锁的数量大于最大数（65535，2的16次方-1）就抛出一个Error。
            throw new Error("Maximum lock count exceeded");
        // Reentrant acquire
        setState(c + acquires);
        return true;
    }
    if (writerShouldBlock() ||
        !compareAndSetState(c, c + acquires))//如果c=0,即写线程数为0，锁的数量为0，若当前线程需要阻塞那么就返回失败或通过CAS增加写线程数失败也返回失败。
        return false;
    setExclusiveOwnerThread(current);
    return true;
}

```

执行步骤

1. 由c = getState()获取锁的个数，然后再通过c来获取写锁的个数w。因为写锁是低16位，所以取低16位的最大值与当前的c做与运算**(c & EXCLUSIVE_MASK)**，高16位和0与运算后是0，剩下的就是低位运算的值，同时也是持有写锁的线程数目。
2. 若写锁数量为0或持有锁的线程不是当前线程就返回失败。
3. 如果写入锁的数量大于最大数（65535，2的16次方-1）就抛出一个Error。
4. 如果c=0,即写线程数为0，锁的数量为0，若当前线程需要阻塞那么就返回失败或通过CAS增加写线程数失败也返回失败。
5. 如果c=0,w=0或者c>0,w>0（重入），则设置当前线程或锁的拥有者，返回成功。

## 自旋锁和适应性自旋锁

### 原理

自旋锁的实现原理同样是CAS，AtomicInteger中调用unsafe进行自增操作的源码中的do-while循环就是一个自旋操作，如果修改数值失败则通过循环来执行自旋，直至修改成功。

```java
public final int getAndAddInt(Object var1, long var2, int var4) {
        int var5;
        do {
            var5 = this.getIntVolatile(var1, var2);
        } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));//若var2与var5不相等，则一直自旋。若相等更新它的值为var5 + var4

        return var5;//返回原来的值
    }
```



### 自旋锁

自旋锁是为了减少cpu来回切换和恢复现场的消耗，通过让当前线程自旋，直到锁定同步资源的线程释放锁，那么当前线程就不用阻塞，而直接可以获取同步资源。自旋等待虽然避免了线程切换的开销，但它要占用处理器时间。如果锁被占用的时间很短，自旋等待的效果就会非常好。反之，如果锁被占用的时间很长，那么自旋的线程只会白浪费处理器资源。所以，自旋等待的时间必须要有一定的限度，如果自旋超过了限定次数（默认是10次，可以使用-XX:PreBlockSpin来更改）没有成功获得锁，就应当挂起线程。

### 适应性自旋锁

适应性自旋锁的自旋的时间（次数）不再固定，而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定。如果在同一个锁对象上，自旋等待刚刚成功获得过锁，并且持有锁的线程正在运行中，那么虚拟机就会认为这次自旋也是很有可能再次成1功，进而它将允许自旋等待持续相对更长的时间。如果对于某个锁，自旋很少成功获得过，那在以后尝试获取这个锁时将可能省略掉自旋过程，直接阻塞线程，避免浪费处理器资源。

### 存在的问题

1. ABA问题：CAS需要在操作值的时候检查内存值是否发生变化，没有发生变化才会更新内存值。但是如果内存值原来是A，后来变成了B，然后又变成了A，那么CAS进行检查时会发现值没有发生变化，但是实际上是有变化的。ABA问题的解决思路就是在变量前面添加版本号，每次变量更新的时候都把版本号加一，这样变化过程就从“A－B－A”变成了“1A－2B－3A”。
2. 循环时间长开销大：CAS操作如果长时间不成功，会导致其一直自旋，给CPU带来非常大的开销。
3. 只能保证一个共享变量的原子操作：对一个共享变量执行操作时，CAS能够保证原子操作，但是对多个共享变量操作时，CAS是无法保证操作的原子性的。解决方法是用AtomicReference类来保证引用对象之间的原子性，可以把多个变量放在一个对象里来进行CAS操作。

### 例子

![自旋锁](/Users/jiangcheng/Java-Notes/图片/Java并发/自旋锁.png)

## 无锁、偏向锁、轻量级锁、重量级锁

### 无锁

无锁不对资源进行锁定，所有的线程都能访问并修改同一个资源，但同时只有一个线程能修改成功。

#### 优点

1. 无锁的特点就是修改操作在循环内进行，线程会不断的尝试修改共享资源。
2. 如果没有冲突就修改成功并退出，否则就会继续循环尝试。
3. 如果有多个线程修改同一个值，必定会有一个线程能修改成功，而其他修改失败的线程会不断重试直到修改成功。(CAS原理及应用即是无锁的实现)

### 偏向锁

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁，降低获取锁的代价。

#### 优点

1. 在大多数情况下，锁总是由同一线程多次获得，不存在多线程竞争，所以出现了偏向锁。其目标就是在**只有一个线程执行同步代码块时能够提高性能。**
2. 因为轻量级锁的获取及释放依赖多次CAS原子指令，偏向锁可以避免在无多线程竞争的情况下不必要的轻量级锁执行路径。
3. 避免自旋操作，消耗cpu。

#### 原理

当一个线程访问同步代码块并获取锁时，会在Mark Word里存储锁偏向的线程ID。在线程进入和退出同步块时不再通过CAS操作来加锁和解锁，而是检测Mark Word里是否存储着指向当前线程的偏向锁。引入偏向锁是为了在无多线程竞争的情况下尽量减少不必要的轻量级锁执行路径，因为轻量级锁的获取及释放依赖多次CAS原子指令，而偏向锁只需要在置换ThreadID的时候依赖一次CAS原子指令即可。

偏向锁只有遇到其他线程尝试竞争偏向锁时，持有偏向锁的线程才会释放锁，线程不会主动释放偏向锁。偏向锁的撤销，需要等待全局安全点（在这个时间点上没有字节码正在执行），它会首先暂停拥有偏向锁的线程，判断锁对象是否处于被锁定状态。撤销偏向锁后恢复到无锁（标志位为“01”）或轻量级锁（标志位为“00”）的状态。

#### 使用方法

在JDK 6及以后的JVM里是默认启用的。可以通过JVM参数关闭偏向锁：-XX:-UseBiasedLocking=false，关闭之后程序默认会进入轻量级锁状态。

### 轻量级锁

当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，从而提高性能。

#### 优点

1. 通过自旋，减少cpu切换的消耗。
2. 避免线程阻塞和唤醒而影响性能

#### 原理

在代码进入同步块的时候，如果同步对象锁状态为无锁状态（锁标志位为“01”状态，是否为偏向锁为“0”），虚拟机首先将在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝，然后拷贝对象头中的Mark Word复制到锁记录中。拷贝成功后，虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock Record里的owner指针指向对象的Mark Word。如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，表示此对象处于轻量级锁定状态。

如果轻量级锁的更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行，否则说明多个线程竞争锁。

若当前只有一个等待线程，则该线程通过自旋进行等待。但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。

### 重量级锁

变为重量级锁时，锁标志的状态值变为“10”，此时Mark Word中存储的是指向重量级锁的指针，此时等待锁的线程都会进入阻塞状态。



### 锁转换流程(synchronized锁状态转换)

 无锁---->偏向锁---->轻量级锁---->重量级锁

![锁状态转换](/Users/jiangcheng/Java-Notes/图片/Java并发/锁状态转换.png)

偏向锁通过对比Mark Word解决加锁问题，避免执行CAS操作。而轻量级锁是通过用CAS操作和自旋来解决加锁问题，避免线程阻塞和唤醒而影响性能。重量级锁是将除了拥有锁的线程以外的线程都阻塞。

## 可重入锁和非可重入锁

### 可重入锁

是指在同一个线程在外层方法获取锁的时候，再进入该线程的内层方法会自动获取锁（前提锁对象得是同一个对象或者class），不会因为之前已经获取过还没释放而阻塞。Java中ReentrantLock和synchronized都是可重入锁。

```java
public class Demo {
    public synchronized void A() {
        System.out.println("方法A执行...");
        B();
    }

    public synchronized void B() {
        System.out.println("方法B执行...");
    }
}
```

上面代码中，类中的两个方法都是被内置锁synchronized修饰的，A()方法中调用B()方法。因为内置锁是可重入的，所以同一个线程在调用B()时可以直接获得当前对象的锁，进入B()进行操作。

#### 优点

可一定程度避免死锁

#### 例子

![可重入锁](/Users/jiangcheng/Java-Notes/图片/Java并发/可重入锁.png)

有多个学生在排队打饭，此时饭堂阿姨允许一个人打多份饭，第一个饭盒与锁绑定后开始打饭，第二个饭盒也可以直接与锁绑定打饭，第一个人所有饭盒打完饭后，才把锁还给饭堂阿姨。这个人的所有打饭流程都能够成功执行，后续等待的人也能够打到饭。这就是可重入锁。

### 非可重入锁

当前线程在调用B之前需要将执行A()时获取当前对象的锁释放掉，实际上该对象锁已被当前线程所持有，且无法释放。所以此时会出现死锁。

#### 例子

![不可重入锁](/Users/jiangcheng/Java-Notes/图片/Java并发/不可重入锁.png)

此时饭堂阿姨只允许锁和同一个人的一个饭盒绑定打饭。第一个饭盒和锁绑定打完饭之后并不会释放锁，导致第二个饭盒不能和锁绑定也无法打饭。当前线程出现死锁，整个等待队列中的所有线程都无法被唤醒。

# CAS的含义 ABA问题 

CAS是让CPU比较内存中某个值是否和预期的值相同，如果相同则将这个值更新为新值，不相同则不做更新，也就是CAS是**原子性**的操作(读和写两者同时具有原子性)，其实现方式是通过借助`C/C++`调用CPU指令完成的，所以效率很高。

```java
public boolean compareAndSwap(int value, int expect, int update) {
//        如果内存中的值value和期望值expect一样 则将值更新为新值update
    if (value == expect) {
        value = update;
        return true;
    } else {
        return false;
    }
}

```

## ABA问题

在多线程场景下`CAS`会出现`ABA`问题，例如有2个线程同时对同一个值(初始值为A)进行CAS操作，这三个线程如下

1. 线程1，期望值为A，欲更新的值为B
2. 线程2，期望值为A，欲更新的值为B

线程`1`抢先获得CPU时间片，而线程`2`因为其他原因阻塞了，线程`1`取值与期望的A值比较，发现相等然后将值更新为B，然后这个时候**出现了线程`3`，期望值为B，欲更新的值为A**，线程3取值与期望的值B比较，发现相等则将值更新为A，此时线程`2`从阻塞中恢复，并且获得了CPU时间片，这时候线程`2`取值与期望的值A比较，发现相等则将值更新为B，虽然线程`2`也完成了操作，但是线程`2`并不知道值已经经过了`A->B->A`的变化过程。

## 解决方法

在变量前面加上版本号，每次变量更新的时候变量的**版本号都`+1`**，即`A->B->A`就变成了`1A->2B->3A`

# volatile关键字 

volatile关键字是一个轻量级的锁，可以保证可见性和有序性，但是不保证原子性。

## 实现原理

下面这段话摘自《深入理解Java虚拟机》：

　“**观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现，加入volatile关键字时，会多出一个lock前缀指令**”

* lock前缀指令实际上相当于一个内存屏障（也成内存栅栏），内存屏障会提供3个功能：

1. 它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成；(**防止指令重排**)
2. 如果是写操作，它会导致其他CPU中对应的缓存行无效，并且它会强制当前线程将对缓存的修改操作立即写入主存。(**保持变量的可见性**)

## volatile特点

1. volatile 可以保证**主内存和工作内存直接产生交互**，进行读写操作，保证可见性
2. volatile 仅能保证变量**写操作的原子性**，不能保证读写操作的原子性。
3. volatile可以**禁止指令重排序**（通过插入内存屏障），典型案例是在单例模式中使用。

## volatile变量的开销

volatile不会导致线程上下文切换，但是其读取变量的成本较高，因为其每次都需要从高速缓存或者主内存中读取，无法直接从寄存器中读取变量。

## volatile在什么情况下可以替代锁

olatile是一个轻量级的锁，适合多个线程**共享一个状态变量**，锁适合多个线程**共享一组状态变量**。可以**将多个线程共享的一组状态变量合并成一个对象**，用一个volatile变量来引用该对象，从而替代锁。

# 说一下volatile 指令不可重排 怎么不可重排，为什么不可重排，重排会发生什么

举例：当Instance instance = new Instance()，Jvm执行了以下操作

1. 在堆内存上分配对象的内存空间
2. 在堆内存上初始化对象
3. 设置instance指向刚分配的内存地址

第二步和第三步可能会发生重排序，导致引用型变量指向了一个不为null但是也不完整的对象。（在多线程下的单例模式中，必须通过volatile来禁止指令重排序

# synchronized三种使用方式

它可以使用在方法上和方法块上，表示同步方法和同步代码块

# synchronized实现原理

**synchronized是Java中的一个关键字，是一个内部锁**。它可以使用在方法上和方法块上，表示同步方法和同步代码块。在多线程环境下，同步方法或者同步代码块在同一时刻只允许有一个线程在执行，其余线程都在等待获取锁，也就是实现了整体并发中的局部串行。

## 实现原理

1. 进入时，执行**monitorenter**，将计数器+1，释放锁**monitorexit**时，计数器-1
2. 当一个线程判断到计数器为0时，则当前锁空闲，可以占用；反之，当前线程进入等待状态

# synchronized和ReentrantLock的区别

ReentrantLock**是显示锁**，其提供了一些内部锁不具备的特性，但并不是内部锁的替代品。**显式锁支持公平和非公平的调度方式**，默认采用非公平调度。

synchronized 内部锁简单，但是不灵活。显示锁支持在一个方法内申请锁，并且在另一个方法里释放锁。**显示锁定义了一个tryLock（）方法，尝试去获取锁**，成功返回true，失败并不会导致其执行的线程被暂停而是直接返回false，即可以**避免死锁**。 

# AQS（AbstractQueuedSynchronizer）的原理与实现。

后续完善

# CountDownLatch、CyclicBarrier介绍

## CountDownLatch

**CountDownLatch**是一个**倒计时协调器**，它可以实现一个或者多个线程等待其余线程完成一组特定的操作之后，继续运行。

### 内部实现原理

1. **CountDownLatch**内部维护一个计数器，CountDownLatch.countDown（）每被执行一次都会使计数器值减少1。
2. **当计数器不为0时**，CountDownLatch.await（）方法的调用将会导致执行线程被暂停，这些线程就叫做该CountDownLatch上的等待线程。
3. CountDownLatch.countDown（）相当于一个通知方法，**当计数器值达到0时**，唤醒所有等待线程。当然对应还有指定等待时间长度的CountDownLatch.await( long , TimeUnit)方法。

## CyclicBarrier

**CyclicBarrier**是**一个栅栏**，可以实现多个线程相互等待执行到指定的地点，这时候这些线程会再接着执行，在实际工作中可以用来**模拟高并发请求测试**。

### 内部实现原理

1. 使用CyclicBarrier实现等待的线程被称为**参与方（Party）**，参与方只需要执行CyclicBarrier.await（）就可以实现等待，**该栅栏维护了一个显示锁**，可以识别出最后一个参与方，**当最后一个参与方调用await（）方法时**，前面等待的参与方都会被唤醒，并且该最后一个参与方也不会被暂停。
2. **CyclicBarrier内部维护了一个计数器变量count = 参与方的个数**，调用await方法可以使得count -1。**当判断到是最后一个参与方时**，调用singalAll唤醒所有线程。

# JUC原子类  

介绍Atomic之前先来看一个问题吧，**i++操作是线程安全的吗**？

i++操作并不是线程安全的，它是一个复合操作，包含三个步骤：

- **拷贝i的值到临时变量**
- **临时变量++操作**
- **拷贝回原始变量i**



这是一个**复合操作，不能保证原子性**，所以这不是线程安全的操作。**那么如何实现原子自增等操作呢？**

这里就用到了JDK在java.util.concurrent.atomic包下的AtomicInteger等原子类了。**AtomicInteger类提供了getAndIncrement和incrementAndGet等原子性的自增自减等操作**。**Atomic等原子类内部使用了CAS来保证原子性。**

# 两个线程读，一个线程写，如何实现，不用锁可以吗

可以，使用volatile

# 线程池作用、参数、有几种 

## 作用

1. 线程池可以重复利用已创建的线程，一次创建可以执行多次任务，有效降低线程创建和销毁所造成的资源消耗；
2. 线程池技术使得请求可以快速得到响应，节约了创建线程的时间；
3. 线程的创建需要占用系统内存，消耗系统资源，使用线程池可以更好的管理线程，做到统一分配、调优和监控线程，提高系统的稳定性。

## 参数

```java
 public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```

1. **corePoolSize：核心线程数**
2. **maximumPoolSize：最大线程数**
3. **keepAliveTime ：线程空闲但是保持不被回收的时间**
4. **unit：时间单位**
5. **workQueue：存储线程的队列**
6. **threadFactory：创建线程的工厂**
7. **handler：拒绝策略**

# 常见的线程池类型

1. **newCachedThreadPool( )**
   1. 核心线程池大小为0，最大线程池大小不受限，来一个创建一个线程
   2. 适合用来执行大量耗时较短且提交频率较高的任务
2. **newFixedThreadPool( )**
   1. 固定大小的线程池
   2. 当线程池大小达到核心线程池大小，就不会增加也不会减小工作者线程的固定大小的线程池
3. **newSingleThreadExecutor( )**
   1. 便于实现单（多）生产者-消费者模式

# workQueue阻塞队列

1. ArrayBlockingQueue
   1. 内部使用一个**数组**作为其存储空间，数组的存储空间是**预先分配**的
   2. **优点是** put 和 take操作不会增加GC的负担（因为空间是预先分配的）
   3. **缺点是** put 和 take操作使用同一个锁，可能导致锁争用，导致较多的上下文切换。
   4. ArrayBlockingQueue适合在生产者线程和消费者线程之间的**并发程序较低**的情况下使用。
2. LinkedBlockingQueue
   1. 是一个无界队列（其实队列长度是Integer.MAX_VALUE）
   2. 内部存储空间是一个**链表**，并且链表节点所需的**存储空间是动态分配**的
   3. **优点是** put 和 take 操作使用两个显式锁（putLock和takeLock）
   4. **缺点是**增加了GC的负担，因为空间是动态分配的。
   5. LinkedBlockingQueue适合在生产者线程和消费者线程之间的并发程序较高的情况下使用。
3. SynchronousQueue
   1. SynchronousQueue可以被看做一种特殊的有界队列。生产者线程生产一个产品之后，会等待消费者线程来取走这个产品，才会接着生产下一个产品，适合在生产者线程和消费者线程之间的处理能力相差不大的情况下使用。

# 拒绝策略有几种

1. 如果运行的线程少于corePoolSize，则Executor始终首选添加新的线程，而不进行排队
2. 如果运行的线程等于或者多于corePoolSize，则Executor始终首选将请求加入队列，而不是添加新线程
3. 如果无法将请求加入队列，即队列已经满了，则创建新的线程，除非创建此线程超出maxinumPoolSize，在这种情况下，任务默认将被拒绝。

# Java中如何正常终止线程

## Stop方法

stop方法已经被废弃。原因是stop()方法会强行把执行一半的线程终止。这样会就不会保证线程的资源正确释放，通常是没有给与线程完成资源释放工作的机会，因此会导致程序工作在不确定的状态下。

例子：

```java
public class Demo {

    public static User user = new User();

    // 改变user变量的线程
    public static class ChangeObjectThread extends Thread {
        @Override
        public void run() {
            while (true) {
                synchronized (Demo.class) {
                    int v = (int) (System.currentTimeMillis() / 1000);
                    user.setId(v);
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    user.setName(String.valueOf(v));
                }
                // 让出CPU，给其他线程执行
                Thread.yield();
            }

        }

    }
    // 读取user变量的线程
    public static class ReadObjectThread extends Thread {
        @Override
        public void run() {
            while (true) {
                synchronized (Demo.class) {
                    if (user.getId() != Integer.parseInt(user.getName())) {
                        System.out.println(user.toString());
                    }
                }
                // 让出CPU，给其他线程执行
                Thread.yield();
            }

        }
    }

    public static void main(String[] args) throws InterruptedException {
        new ReadObjectThread().start();
        while (true) {
            Thread t = new ChangeObjectThread();
            t.start();
            Thread.sleep(150);
            //使用stop()方法，强制停止线程
            t.stop();
        }

    }
}

class User {
    private int id;
    private String name;

    public User() {
        this(0, "0");
    }

    public User(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}

/**输出
User{id=1601881231, name='1601881230'}
User{id=1601881231, name='1601881230'}
User{id=1601881232, name='1601881231'}
User{id=1601881233, name='1601881232'}
User{id=1601881233, name='1601881232'}
User{id=1601881233, name='1601881232'}

*/
```

当执行ReadObjectThread方法的时候，如果id和name不一样就会输出这个对象。ChangeObjectThread总是写入连个相同的数值，但是在代码中因为使用了stop()强行停止线程，造成了数据的不同步。

## 利用boolean变量中止线程

如果需要停止一个线程时，只是需要执行时确定线程什么时候退出就可以了。

```java
class TheadDemo implements Runnable {
    boolean isBreak=false;

    public void setBreak() {
        isBreak = true;
    }
    @Override
    public void run() {
        while (!isBreak) {
            synchronized (Demo.class) {
                //TODO
            }
            Thread.yield();
        }
    }
}
```

## interrupt方法

中断可以理解为线程的一个标识位属性，它表示一个运行中的线程是否被其他线程进行了中断操作。其他线程通过调用该线程的interrupt()方法对其进行中断操作。

线程通过方法isInterrupted()来进行判断是否被中断，也可以调用静态方法Thread.interrupted()对当前线程的中断标识位进行复位。如果该线程已经处于终结状态，即使该线程被中断过，在调用该线程对象的isInterrupted()时依旧会返回false。

```java
public class Demo {
    public static void main(String[] args) throws Exception {
        // ThreadA睡眠10s
        Thread threadA = new Thread(new ThreadA(), "ThreadA");
        threadA.setDaemon(true);
        // ThreadB不停的运行
        Thread threadB = new Thread(new ThreadB(), "ThreadB");
        threadB.setDaemon(true);
        threadA.start();
        threadB.start();
        // 休眠5秒，让sleepThread和busyThread充分运行
        TimeUnit.SECONDS.sleep(5);
        threadA.interrupt();
        threadB.interrupt();
        System.out.println("threadA interrupted is "
                + threadA.isInterrupted());
        System.out.println("threadB interrupted is "
                + threadB.isInterrupted());
        // 防止sleepThread和busyThread立刻退出
        TimeUnit.SECONDS.sleep(2);
    }

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            while (true) {
                try {
                    TimeUnit.SECONDS.sleep(10);
                } catch (InterruptedException e) {
                    //e.printStackTrace();
                }
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            while (true) {
            }
        }
    }
}
/**输出
ThreadA interrupted is false
ThreadB interrupted is true
/
```

由结果得知，在threadA运行时被中断会许多声明抛出InterruptedException的方法这，Java虚拟机会在这方法在抛出InterruptedException之前先将该线程的中断标识位清除，然后抛出InterruptedException，此时调用isInterrupted()方法将会返回false。而threadB则被正常中断，isInterrupted()方法返回true。



## 利用中止机制中断线程

```java
public class Demo {
    public static void main(String[] args) throws Exception {
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    // 使用中断机制，来终止线程
                    if (Thread.currentThread().isInterrupted()) {
                        System.out.println("Interrupted");
                        break;
                    }
                    try {
                        Thread.sleep(300);
                    } catch (InterruptedException e) {
                        System.out.println(e.getMessage());
                        Thread.currentThread().interrupt();
                    }
                }
            }
        });
        thread.start();
        Thread.sleep(200);
        thread.interrupt();

    }

}
/**
sleep interrupted
Interrupted
/
```



# 主线程可以捕获子线程抛出的异常吗 

可以，代码如下

```java
public class Main {

    public static void main(String[] args) {
        try {
            Thread thread = new Thread(new ThreadExceptionRunner());
            thread.start();
        } catch (Exception e) {
            System.out.println("-------");
            e.printStackTrace();
        }finally {
            
        }
        System.out.println("开始捕捉子线程的异常");
        ExecutorService executorService= Executors.newCachedThreadPool(new HandleThreadFactory());
        executorService.execute(new ThreadExceptionRunner());
        executorService.shutdown();

    }
}

class ThreadExceptionRunner implements Runnable {
    @Override
    public void run() {
        throw new RuntimeException("error !");
    }
}

class MyUncaughtExceptionHandle implements Thread.UncaughtExceptionHandler {

    @Override
    public void uncaughtException(Thread t, Throwable e) {
        System.out.println("Caught Error：" + e);

    }
}
class HandleThreadFactory implements ThreadFactory {
    @Override
    public Thread newThread(Runnable r) {
        System.out.println("create thread t");
        Thread t = new Thread(r);
        System.out.println("set uncaughtException for t");
        t.setUncaughtExceptionHandler(new MyUncaughtExceptionHandle());
        return t;
    }
}

/** 输出
Exception in thread "Thread-0" java.lang.RuntimeException: error !
	at 测试.ThreadExceptionRunner.run(Main.java:31)
	at java.lang.Thread.run(Thread.java:748)
开始捕捉子线程的异常
create thread t
set uncaughtException for t
Caught Error：java.lang.RuntimeException: error !
/
```

