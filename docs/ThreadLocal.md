

# 类结构

```java
public class ThreadLocal<T>
```

1. ThreadLocal 定义类时带有泛型，说明 ThreadLocal 可以储存任意格式的数据。
2. 只要该线程处于活动状态并且实例是可访问的，每个线程都隐式引用threadlocal副本。
3. 只要线程消失，其所有threadlocal本地实例副本会受到垃圾回收(除非存在其他对这些副本的引用)

# 关键属性

## 基本属性

```java
// threadLocalHashCode 表示当前 ThreadLocal 的 hashCode，用于计算当前 ThreadLocal 在 ThreadLocalMap 中的索引位置
private final int threadLocalHashCode = nextHashCode();
//下一个hash值，这个值是从零开始
//static + AtomicInteger 保证了在一台机器中每个 ThreadLocal 的 threadLocalHashCode 是唯一的
//被 static 修饰非常关键，因为一个线程在处理业务的过程中，ThreadLocalMap 是会被 set 多个 ThreadLocal 的，多个 ThreadLocal 就依靠 threadLocalHashCode 进行区分
private static AtomicInteger nextHashCode =new AtomicInteger();
// 计算 ThreadLocal 的 hashCode 值(就是递增)，
private static int nextHashCode() {
        return nextHashCode.getAndAdd(HASH_INCREMENT);
    }

```

## ThreadLocalMap

1. ThreadLocalMap作为容器，管理线程中多个 ThreadLocal。
2. ThreadLocalMap是仅适用于维护线程本地的值。 

```java
static class ThreadLocalMap{
   // 数组中的每个节点值，WeakReference 是弱引用，当没有引用指向时，会直接被回收
   static class Entry extends WeakReference<ThreadLocal<?>> {
             // 当前 ThreadLocal 关联的值
            Object value;
 			// WeakReference 的引用 referent 就是 ThreadLocal
            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
  // 数组的初始化大小
  private static final int INITIAL_CAPACITY = 16;
  
  // 存储 ThreadLocal 的数组
  private Entry[] table;
  // 扩容的阈值，默认是数组大小的三分之二
  private int threshold;
}
```

# ThreadLocal 是如何做到线程之间数据隔离的

```java
/*  
ThreadLocal类
*/
static ThreadLocalMap createInheritedMap(ThreadLocalMap parentMap) {
        return new ThreadLocalMap(parentMap);
    }
```



```java
/*  
Thread类 的属性
*/

ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;

ThreadLocal.ThreadLocalMap threadLocals = null;

/*  
Thread类 的init方法部分源码

       @param g  线程组
       @param target Runnable里面的run方法
       @param name 新线程的名字
       @param stackSize 新线程栈的大小，改参数可以省略
      @param inheritThreadLocals 如果为true 则继承来自threadlocal创建的线程
*/
private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize, AccessControlContext acc,
                      boolean inheritThreadLocals) {
       
  //当父的inheritableThreadLocals不为null，会把inheritableThreadLocals的值全部传递给子线程
        if (inheritThreadLocals && parent.inheritableThreadLocals != null)
            this.inheritableThreadLocals =
                ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);
        this.stackSize = stackSize;
        tid = nextThreadID();
    }
```

可以从Thread源码得知ThreadLocals.ThreadLocalMap 和 InheritableThreadLocals.ThreadLocalMap 分别是线程的属性，所以每个线程的 ThreadLocals 都是隔离独享的。然后父线程在创建子线程的情况下，会拷贝 inheritableThreadLocals 的值，但不会拷贝 threadLocals 的值，因此保证了线程之间数据隔离。

# 重要的方法

## Set方法

这是 ThreadLocal 的set方法

```java
// set 操作每个线程都是串行的，不会有线程安全的问题
public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
  // 当前 thradLocal 之前有设置值，直接设置，否则初始化
        if (map != null)
            map.set(this, value);
  // 初始化ThreadLocalMap
        else
            createMap(t, value);
    }
```

 ThreadLocalMap的set方法

```java
private void set(ThreadLocal<?> key, Object value) {
            Entry[] tab = table;
            int len = tab.length;
  // 计算key在数组中的下标，其实就是 ThreadLocal 的 hashCode 和数组大小-1取余，与HashMap 求。数组下标一样
            int i = key.threadLocalHashCode & (len-1);
			// 查看 i 索引位置有没有值，有值的话，索引位置 + 1，直到找到没有值的位置，防止hash冲突
    // 这种解决 hash 冲突的策略，也导致了其在 get 时查找策略有所不同，体现在 getEntryAfterMiss 中
            for (Entry e = tab[i];
                 e != null;
           // nextIndex 就是让在不超过数组长度的基础上，把数组的索引位置 + 1
                 e = tab[i = nextIndex(i, len)]) {
                ThreadLocal<?> k = e.get();
					    // 找到内存地址一样的 ThreadLocal，直接替换
                if (k == key) {
                    e.value = value;
                    return;
                }
// 当前 key 是 null，说明 ThreadLocal 被回收了，直接替换掉
                if (k == null) {
                    replaceStaleEntry(key, value, i);
                    return;
                }
            }
// 当前 i 位置是无值的，可以被当前 thradLocal 使用
            tab[i] = new Entry(key, value);
            int sz = ++size;
  // 当数组大小大于等于扩容阈值(数组大小的三分之二)时，进行扩容
            if (!cleanSomeSlots(i, sz) && sz >= threshold)
                rehash();
        }
```

1.  通过基本属性得知 ThreadLocal 的 hashCode 是通过 AtomicInteger 递增的。
2. 通过 hashCode 计算的索引位置 i 处如果已经有值了，会从 i 开始，通过 +1 不断的往后寻找，直到找到索引位置为空的地方，把当前 ThreadLocal 作为 key 放进去。
3. 计算数组索引位置的公式是：hashCode 取模数组大小，由于 hashCode 不断自增，所以不同的 hashCode 大概率上会计算到同一个数组的索引位置，发生hash冲突。

## get方法

这是 ThreadLocal 的get方法

```java 
public T get() {
  //得到当前线程
        Thread t = Thread.currentThread();
   // 从当前线程中拿到 ThreadLocalMap
        ThreadLocalMap map = getMap(t);
        if (map != null) {
   // 从 map 中拿到 entry，由于 ThreadLocalMap 在 set 时的 hash 冲突的策略不同，导致拿的时候逻辑也不太一样
            ThreadLocalMap.Entry e = map.getEntry(this);
          // 如果不为空，读取当前 ThreadLocal 中保存的值
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
   // 否则给当前线程的 ThreadLocal 初始化，并返回初始值 null
        return setInitialValue();
    }

```

ThreadLocalMap的getEntry方法

```java
// 得到当前 thradLocal 的key对应的值，值的类型是由 thradLocal 的泛型决定的
//由于 thradLocalMap set 时解决数组索引位置冲突的逻辑，导致 thradLocalMap get 时的逻辑也是对应的
private Entry getEntry(ThreadLocal<?> key) {
  //首先尝试根据 hashcode 取模数组大小-1 ，找到索引位置 i。若找不到的话，自旋把 i+1，直到找到索引位置不为空为止
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
   // e 不为空，并且 e 的 ThreadLocal 的内存地址和 key 相同，直接返回，否则就是没有找到，继续通过 getEntryAfterMiss 方法找
    if (e != null && e.get() == key)
        return e;
    else
   // 这个取数据的逻辑，是因为 set 时数组索引位置冲突造成的  
        return getEntryAfterMiss(key, i, e);
}
```

getEntryAfterMiss方法

```java
private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
            Entry[] tab = table;
            int len = tab.length;
			//一直自旋加1，直到知道为止
            while (e != null) {
                ThreadLocal<?> k = e.get();
                if (k == key)
                  //内存地址一样，直接返回	
                    return e;
                if (k == null)
                  // 删除没用的 key
                    expungeStaleEntry(i);
                else
                  // 继续使索引位置 + 1，
                    i = nextIndex(i, len);
                e = tab[i];
            }
            return null;
        }

```

## resize方法

```java
private void resize() {
   // 拿出旧的数组
    Entry[] oldTab = table;
   // 旧数组长度
    int oldLen = oldTab.length;
  //扩容为原来的两倍
    int newLen = oldLen * 2;
  // 初始化新数组
    Entry[] newTab = new Entry[newLen];
    int count = 0;

    for (int j = 0; j < oldLen; ++j) {
        Entry e = oldTab[j];
        if (e != null) {
            ThreadLocal<?> k = e.get();
            if (k == null) {
                e.value = null; // Help the GC
            } else {
                // 计算 ThreadLocal 在新数组中的位置
                int h = k.threadLocalHashCode & (newLen - 1);
              //若这个位置已经有了值，则一直自旋，完后加1，知道没有值位置
                while (newTab[h] != null)
                    h = nextIndex(h, newLen);
              // 给新数组赋值
                newTab[h] = e;
                count++;
            }
        }
    }
 // 给新数组初始化下次扩容阈值，为数组长度的三分之二
    setThreshold(newLen);
    size = count;
    table = newTab;
}
```

setThreshold源码

```
private void setThreshold(int len) {
    threshold = len * 2 / 3;
}
```

# Demo

```java
public class Main {

    static final ThreadLocal<Map<String, String>> context = new ThreadLocal<>();

    public static void main(String[] args) {
        Map<String, String> contextMap = context.get();

        contextMap = new HashMap<>();

        contextMap.put("key1", "value1");
        context.set(contextMap);
        System.out.println("key1，value1被放到上下文中");
        getFromComtext();
    }
    private static String getFromComtext() {
        String value1 = context.get().get("key1");
        System.out.println("从 ThreadLocal 中取出上下文，key1 对应的值为" + value1);
        return value1;
    }
}

/**
	输出为
	key1，value1被放到上下文中
	从 ThreadLocal 中取出上下文，key1 对应的值为value1

*/
```

