

# List、set、map三者的区别

# ArrayList和LinkedList的区别

# HashMap源码说一下（细说，从初始化到put、get、扩容、[红黑树]()、1.8做了哪些优化）  

#  Hashmap[链表]()要转[红黑树]()，为什么长度超过8转 

#  多线程Put可能发生的问题 

#  ConcurrentHashMap的实现 

#  Concurranthashmap和Hashtable的区别 

#  concurrenthashmap的 size如何计算 

#  LinkedHashMap，如何在o（1）的时间里面删除一个元素 

#  Hashset和hashmap的区别 

#  HashTable与hashMap的不同 

#  TreeMap 

#  List遍历时删除元素 

#  快速失败（fail—fast）、安全失败（fail—safe） 

#  CopyOnWriteArrayList 

#  HashMap遍历时删除元素

 

# HashMap实现流程

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)//如果是单个节点
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)//如果是红黑树
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { //如果是链表
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, loTail = null;
                  //loHead,loTail和hiHead,loTail主要是把原链表拆分成两个链表
                  /*例子
                  	扩容前,当前容量为8       扩容后,当前容量为16
                  	桶的序号为2						 桶的序号为2
                  	2->10->18->26					2->18
                  												桶的序号为10
                  												10->26
                  
                  */
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {//相当于e.hash%(oldCap-1)，选择出扩容后在同一个桶中的节点
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

步骤

1. 执行put的时候，判断数组是否为空，如果为空，就初始化数组，默认数组大小为16，扩容阈值为12.
2. 执行完put后，若数组要扩容，判断旧数组长度是否大于MAXIMUM_CAPACITY，若大于就直接返回不扩容。若小于MAXIMUM_CAPACITY，创建一个新数组，新数组容量为旧数组的两倍，并更新扩容阈值为原来的两倍。
3. 然后把旧数组的值复制到新数组上。
4. 遍历旧数组的每个Node，若当前Node为单个节点，直接复制到新数组；若当前Node为为红黑树，就调用((TreeNode<K,V>)e).split(this, newTab, j, oldCap)，若为链表，就把当前Node链表拆分后复制到新数组。
5. 最后令旧数组为null，方便进行垃圾回收。





# Map链表的头插法

如果两个线程都发现HashMap需要重新调整大小，那么它们会同时试着去调整大小。在调整大小时，存储在链表中的元素的次序会反过来，因为在放入新的位置时，HashMap会将Entry对象不断的插入链表的头部。插入头部也主要是为了防止尾部遍历，否则这对key的HashCode相同的Entry每次添加还要定位到尾节点。如果条件竞争发送了，可能会出现环形链表，之后当我们get(key)操作时，就有可能发生死循环。