---
layout:     post
title:      "Java 多线程笔记"
subtitle:   ""
date:       2015-01-29 12:00:00
author:     "Tidycoder"
header-img: "img/post-bg-2015.jpg"
tags:
    - Java
---

### volatile

volatile可以保证数据的在多个线程中的可见性，一致性: 
线程本身并不直接与主内存进行数据的交互，而是通过线程的工作内存来完成相应的操作。这也是导致线程间数据不可见的本质原因。因此要实现volatile变量的可见性，直接从这方面入手即可。对volatile变量的写操作与普通变量的主要区别有两点：
　　（1）修改volatile变量时会强制将修改后的值刷新的主内存中。
　　（2）修改volatile变量后会导致其他线程工作内存中对应的变量值失效。因此，再读取该变量值的时候就需要重新从读取主内存中的值。
　　通过这两个操作，就可以解决volatile变量的可见性问题。

volatile不能保证原子性: 一般在多线程中使用volatile变量，为了安全，对变量的写入操作不能依赖当前变量的值：如Num++或者Num=Num*5这些操作。

### Synchronized
Synchronized可以实现原子性(即互斥性) 和 可见性。
Synchronized实现原理如下：
线程解锁前，必须把共享变量的最新值刷新到主内存中
线程加锁前，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的值

线程执行互斥代码的过程:
- 获得互斥锁
- 清空工作内存
- 从主内存拷贝变量的最新副本到工作内存
- 执行代码
- 将更改后的共享变量的值刷新到主内存
- 释放互斥锁

### Synchronized和Volatile的比较
    1）Synchronized保证内存可见性和操作的原子性
    2）Volatile只能保证内存可见性
    3）Volatile不需要加锁，比Synchronized更轻量级，并不会阻塞线程（volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞。）
    4）volatile标记的变量不会被编译器优化,而synchronized标记的变量可以被编译器优化（如编译器重排序的优化）.
    5）volatile是变量修饰符，仅能用于变量，而synchronized是一个方法或块的修饰符。
      volatile本质是在告诉JVM当前变量在寄存器中的值是不确定的，使用前，需要先从主存中读取，因此可以实现可见性。而对n=n+1,n++等操作时，volatile关键字将失效，不能起到像synchronized一样的线程同步（原子性）的效果。

### Thread State
![Image of Thread State](https://github.com/tidycoder/blog/blob/master/images/javaThreadState.png?raw=true)

### 线程间通信
wait notify notifyAll
注意：调用notify和notifyAll方法后，当前线程并不会立即放弃锁的持有权，而必须要等待当前同步代码块执行完才会让出锁。

### Lock
ReentrantLock Condition等
ReentrantLock的基础是AQS,AQS的基础是CAS


### java.util.concurrent 中高级的工具：

1. Executor Framework

2. 并发集合 (Concurrent Collection)
ConcurrentHashMap BlockingQueue等

3. 同步器（Synchronizer)
同步器是一些使线程能够等待另一个线程的对象，用来使线程协调动作。
包括常用的Countdown Latch(倒计数锁存器）和Semaphore. 不常用的CyclicBarrier和Exchanger

### java中的乐观锁和悲观锁
乐观锁和悲观锁一般来说是数据库处理事物时的一种概念。
悲观锁：假定会发生并发冲突，已开始就加锁，屏蔽一切可能违反数据完整性的操作。
乐观锁：假设不会发生并发冲突，已开始不加锁，只在提交操作时检查是否违反数据完整性。

Java中的乐观锁和悲观锁：独占锁是一种悲观锁，synchronized就是一种独占锁，它假设最坏的情况，并且只有在确保其它线程不会造成干扰的情况下执行，会导致其它所有需要锁的线程挂起，等待持有锁的线程释放锁。但是，由于在进程挂起和恢复执行过程中存在着很大的开销。当一个线程正在等待锁时，它不能做任何事，所以悲观锁有很大的缺点。举个例子，如果一个线程需要某个资源，但是这个资源的占用时间很短，当线程第一次抢占这个资源时，可能这个资源被占用，如果此时挂起这个线程，可能立刻就发现资源可用，然后又需要花费很长的时间重新抢占锁，时间代价就会非常的高。
所以就有了乐观锁的概念，他的核心思路就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。在上面的例子中，某个线程可以不让出cpu,而是一直while循环，如果失败就重试，直到成功为止。所以，当数据争用不严重时，乐观锁效果更好。比如CAS就是一种乐观锁思想的应用。
JDK1.5中引入了底层的支持，在int、long和对象的引用等类型上都公开了CAS的操作，并且JVM把它们编译为底层硬件提供的最有效的方法，在运行CAS的平台上，运行时把它们编译为相应的机器指令。在java.util.concurrent.atomic包下面的所有的原子变量类型中，比如AtomicInteger，都使用了这些底层的JVM支持为数字类型的引用类型提供一种高效的CAS操作。
在CAS操作中，会出现ABA问题。就是如果V的值先由A变成B，再由B变成A，那么仍然认为是发生了变化，并需要重新执行算法中的步骤。有简单的解决方案：不是更新某个引用的值，而是更新两个值，包括一个引用和一个版本号，即使这个值由A变为B，然后为变为A，版本号也是不同的。AtomicStampedReference和AtomicMarkableReference支持在两个变量上执行原子的条件更新。AtomicStampedReference更新一个“对象-引用”二元组，通过在引用上加上“版本号”，从而避免ABA问题，AtomicMarkableReference将更新一个“对象引用-布尔值”的二元组。