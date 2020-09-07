# 目录

[Callable](https://github.com/aisjca/Java-Notes/blob/master/docs/Future%E5%92%8CExecutorService%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#callable)

[Runnable和Callable区别](https://github.com/aisjca/Java-Notes/blob/master/docs/Future%E5%92%8CExecutorService%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#runnable%E5%92%8Ccallable%E5%8C%BA%E5%88%AB)

[FutureTask](https://github.com/aisjca/Java-Notes/blob/master/docs/Future%E5%92%8CExecutorService%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#futuretask)

[Future](https://github.com/aisjca/Java-Notes/blob/master/docs/Future%E5%92%8CExecutorService%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#future)

[FutureTask源码](https://github.com/aisjca/Java-Notes/blob/master/docs/Future%E5%92%8CExecutorService%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#futuretask%E6%BA%90%E7%A0%81)

# Callable

Callable 是一个接口，约定了线程要做的事情，和 Runnable 一样，不过这个线程任务是有返回值的。

```java
@FunctionalInterface
public interface Callable<V> {
    /**
     	 运行得出结果，如果运行失败会抛出异常
     */
    V call() throws Exception;
}
```

返回值是一个泛型，可以定义成任何类型，但使用的时候，都不会直接使用 Callable，而是会结合 FutureTask 一起使用。

# Runnable和Callable区别

新建任务有两种方式，一种是无返回值的 Runnable，一种是有返回值的 Callable， FutureTask在线程池在提交任务时，针对 Runnable 和 Callable 两种情况提供不同的实现思路。FutureTask 实现了 RunnableFuture 接口，又集合了 Callable（Callable 是 FutureTask 的属性），还提供了两者一系列的转化方法，这样 FutureTask 就统一了 Callable 和 Runnable。



# FutureTask

### Future

查看Future接口源码，根据源码介绍。

1. 定义了异步计算的接口，提供了检查是否计算成功的check()、等完计算完成和得到计算结果的方法。
2. 如果想得到结果，只能用get()方法，此方法(无参方法)会一直阻塞到异步任务计算完成；
3. 取消可以使用cancell()方法，但一旦任务计算完成，就无法被取消了。
4. 如果想实现无结果返回，可以使用Future<?>

```java
public interface Future<V> {

   /**
   	如果任务已经成功了，或已经取消了，是无法再取消的，会直接返回取消成功(true)
		如果任务还没有开始进行时，发起取消，是可以取消成功的。
 		如果取消时，任务已经在运行了，mayInterruptIfRunning 为 true 的话，就可以打断运行中的线程
 		mayInterruptIfRunning 为 false，表示不能打断直接返回
 */
    boolean cancel(boolean mayInterruptIfRunning);

  	//如果线程已经运行结束了，isCancelled 和 isDone 返回的都是 true
    
  	/**
     *如果任务在完成前取消，返回true。
     */
    boolean isCancelled();

    /**
     *线程是否已经运行结束
     */
    boolean isDone();

    /**
     * 等待返回结果
     * 如果任务被取消抛出CancellationException 
     * 如果等待过程中被打断了，抛出InterruptedException 
     */
    V get() throws InterruptedException, ExecutionException;

    /**
     * 等待，但是带有超时时间的，如果超时时间外仍然没有响应，抛 TimeoutException 异常
     */
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

FutureTask 可以当做是线程运行的具体任务。查看源码得知FutureTask实现了RunnableFuture<V>接口

```java
public class FutureTask<V> implements RunnableFuture<V>
```

而RunnableFuture实现了Runnable, Future接口

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}

```

RunnableFuture 接口主要是让 Future 可以对 Runnable 进行管理，可以取消 Runnable，查看 Runnable 是否完成等等。

* 从类定义上可以看出来 FutureTask 实现了 RunnableFuture 接口，也就是说间接实现了 Runnnable 接口（RunnableFuture 实现了 Runnnable 接口），就是说 FutureTask 本身就是个 Runnnable，同时 FutureTask 也实现了 Future，根据根据上面所有的Future源码，Future 具备对任务进行管理的功能， 因此FutureTask 也具备对任务进行管理的功能。



### FutureTask源码

#### FutureTask基本属性

```java
public class FutureTask<V> implements RunnableFuture<V> {
    /**
     * 线程4种可能的转换状态
     * NEW -> COMPLETING -> NORMAL
     * NEW -> COMPLETING -> EXCEPTIONAL
     * NEW -> CANCELLED
     * NEW -> INTERRUPTING -> INTERRUPTED
     */
    private volatile int state;								//标记任务的状态
    private static final int NEW          = 0;//线程任务创建
		private static final int COMPLETING   = 1;//任务执行中
		private static final int NORMAL       = 2;//任务执行结束
		private static final int EXCEPTIONAL  = 3;//任务异常
		private static final int CANCELLED    = 4;//任务取消成功
		private static final int INTERRUPTING = 5;//任务正在被打断中
		private static final int INTERRUPTED  = 6;//任务被打断成功
    /** 组合了 Callable,调用后便会变成null */
    private Callable<V> callable;
    /**异步线程返回的结果 */
    private Object outcome; 
    //当前任务所运行的线程
    private volatile Thread runner;
    //记录调用 get 方法时被等待的线程
    private volatile WaitNode waiters;

```

#### FutureTask构造方法

```java

    public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;       // 任务状态初始化
    }

    /**
      使用 Runnable 初始化，并传入 result 作为返回结果。
     	如果不需要返回值，则Future<?> f = new FutureTask<Void>(runnable, null)即可
     */
    public FutureTask(Runnable runnable, V result) {
        this.callable = Executors.callable(runnable, result);
       // 任务状态初始化
        this.state = NEW;       // ensure visibility of callable
    }

```

可以看到this.callable = Executors.callable(runnable, result)就是把入参都转化成 Callable，这主要是因为 Callable 的功能比 Runnnable 丰富，Callable 有返回值，而 Runnnable 没有。

#### callable方法源码和RunnableAdapter源码

上述是使用 Executors.callable 方法来把 Runnnable 转化成 Callable，但是Runnnable 和 Callable 两者都是接口，两者之间是无法进行转化的，因此 Java 新建了一个转化类：RunnableAdapter 来进行转化，以下是源码。

callable方法源码

```java
 public static <T> Callable<T> callable(Runnable task, T result) {
        if (task == null)
            throw new NullPointerException();
        return new RunnableAdapter<T>(task, result);
    }
```

RunnableAdapter源码

```java
static final class RunnableAdapter<T> implements Callable<T> {
        final Runnable task;
        final T result;
        RunnableAdapter(Runnable task, T result) {
            this.task = task;
            this.result = result;
        }
        public T call() {
            task.run();
            return result;
        }
    }
```

由上述源码得知 RunnableAdapter 实现了 Callable接口，所以 RunnableAdapter 就是 Callable，其次 Runnable 是 RunnableAdapter 的一个属性，在构造 RunnableAdapter 的时候会传进来，并且在 call 方法里面调用 Runnable 的 run 方法。这是一个适配模型，把 Runnable 适配成 Callable，首先要实现 Callable 的接口，接着在 Callable 的 call 方法里面调用被适配对象（Runnable）的方法，并返回result。

#### get方法源码

get 有无限阻塞和带超时时间两种方法。

```java
//无限阻塞
public V get() throws InterruptedException, ExecutionException {
        int s = state;
  //如果任务是执行中或者新创建状态，进入awaitDone方法，
        if (s <= COMPLETING)
            s = awaitDone(false, 0L);
        return report(s);
    }
//awaitDone方法在下面
```

```java
//带超时时间
public V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException {
        if (unit == null)
            throw new NullPointerException();
        int s = state;
   // 如果任务已经在执行中了，并且等待一定的时间后，仍然在执行中，直接抛出异常
        if (s <= COMPLETING &&(s = awaitDone(true, unit.toNanos(timeout))) <= COMPLETING)
            throw new TimeoutException();
        return report(s);
    }

//awaitDone方法，等待任务执行完成

private int awaitDone(boolean timed, long nanos)
        throws InterruptedException {
    // 计算等待终止时间，如果timed为false，终止时间为0，表示无限阻塞。如果timed为true的话，终止时间为当前时间加上由get方法传进来的nanos(get方法里面的timeout)
        final long deadline = timed ? System.nanoTime() + nanos : 0L;
        WaitNode q = null;
   // 不排队
        boolean queued = false;
        for (;;) {
          	// 如果线程已经被打断了，删除，抛异常
            if (Thread.interrupted()) {
                removeWaiter(q);
                throw new InterruptedException();
            }

            int s = state;
          // 当前任务已经执行完了，返回
            if (s > COMPLETING) {
              // 当前任务的线程置空
                if (q != null)
                    q.thread = null;
                return s;
            }
           // 如果正在执行，当前线程让出 cpu，重新竞争，防止 cpu 压力过大
            else if (s == COMPLETING) // cannot time out yet
                Thread.yield();
           // 如果第一次运行，新建 waitNode，当前线程就是 waitNode 的属性
            else if (q == null)
                q = new WaitNode();
          // 默认第一次都会执行这里，执行成功之后，queued 就为 true，就不会再执行了
            // 把当前 waitNode 当做 waiters 链表的第一个
            else if (!queued)
                queued = UNSAFE.compareAndSwapObject(this, waitersOffset,
                                                     q.next = waiters, q);
          // 如果设置了超时时间，并过了超时时间的话，从 waiters 链表中删除当前 wait 
          else if (timed) {
                nanos = deadline - System.nanoTime();
                if (nanos <= 0L) {
                    removeWaiter(q);
                    return state;
                }
            // 如果没有超时，则线程进入 TIMED_WAITING 状态
                LockSupport.parkNanos(this, nanos);
            }
          // 没有设置超时时间，进入 WAITING 状态
            else
                LockSupport.park(this);
        }
    }
```

可以看到两个get方法返回值都是report(s)，所以以下是report源码

#### report源码

```java
 private V report(int s) throws ExecutionException {
        Object x = outcome;
   //如果任务正常结束，返回结果x
        if (s == NORMAL)
            return (V)x;
   //如果任务被取消或者被打断，则返回异常
        if (s >= CANCELLED)
            throw new CancellationException();
        throw new ExecutionException((Throwable)x);
    }

```

#### run方法源码

 run 方法可以直接被调用，也可以开启新的线程进行调用

```java
public void run() {
    // 状态不是任务创建，或者当前任务已经有线程在执行了，直接返回
    if (state != NEW ||
        !UNSAFE.compareAndSwapObject(this, runnerOffset,
                                     null, Thread.currentThread()))
        return;
    try {
        Callable<V> c = callable;
        // Callable 不为空，并且状态是新创建的线程
        if (c != null && state == NEW) {
            V result;
            boolean ran;
            try {
                // 调用执行
                result = c.call();
                ran = true;
            } catch (Throwable ex) {
                result = null;
                ran = false;
                setException(ex);
            }
            // 给 outcome 赋值
            if (ran)
                set(result);
        }
    } finally {
        runner = null;
        int s = state;
        if (s >= INTERRUPTING)
            handlePossibleCancellationInterrupt(s);
    }
}
```

如上面所说的run 方法是没有返回值的，通过给 outcome 属性赋值（set(result)），get 时就能从 outcome 属性中拿到返回值。FutureTask 两种构造器，最终都转化成了 Callable，所以在 run 方法执行的时候，只需要执行 Callable 的 call 方法即可。

在执行 c.call() 代码时，如果入参是 Runnable 的话， 调用路径为**FutureTask构造函数里面的Executors.callable -> RunnableAdapter方法里面的call()方法 -> Runnable.run()**，如果入参是 Callable 的话，直接调用。

#### Cancel方法源码

```java
public boolean cancel(boolean mayInterruptIfRunning) {
  //如果任务不是新创建状态并且不能把 new 状态置为取消，直接返回 false
        if (!(state == NEW &&
              UNSAFE.compareAndSwapInt(this, stateOffset, NEW,
                  mayInterruptIfRunning ? INTERRUPTING : CANCELLED)))
            return false;
        try {    // in case call to interrupt throws exception
          // 进行取消操作，打断可能会抛出异常。
            if (mayInterruptIfRunning) {
                try {
                    Thread t = runner;
                    if (t != null)
                        t.interrupt();
                } finally { // final state
                  //状态设置成已打断
                    UNSAFE.putOrderedInt(this, stateOffset, INTERRUPTED);
                }
            }
        } finally {
          // 清理线程
            finishCompletion();
        }
        return true;
    }

```

