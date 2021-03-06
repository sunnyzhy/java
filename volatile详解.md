# 简介
Volatile可以简单的理解为Java提供的一个比Synchronized更加轻量级的同步机制。但是大多数程序员都不能正确、完整的理解和使用它。一般在遇到多线程处理数据竞争时，一律使用Synchronized来进行同步。

但是我们都知道Synchronized是一个重量级的锁，虽然jvm对其进行了大量的优化，但是在volatile相较于synchronized来说是一个轻量级的锁。如果用它来修饰一个变量，那么会比使用synchronized成本更加的底，因为volatile不会引起线程的上下文切换和调度。并且Java语言规范对volatile有如下定义：

```
Java语言是允许线程访问共享变量的，为了确保共享变量能被准确和一致地更新，线程应该确保通过排他锁单独地获取该变量。

Java语言提供了Volatile，在某些情况下比锁妖更加地方便。

如果一个字段被声明成volatile、Java线程内存模型确保所有线程都可以看到该变量地值，并且保持一致性。
```

其实就是Java就是提供了volatile来修饰变量，来确保该变量在线程中是可见的。保证了其原子性。

**那么在什么时候该使用volatile呢？有这么简便轻便的锁为什么还存在并且使用synchronized这种相对而言非常笨重的锁呢？**

# CPU术语与说明
|术语|英文单词|术语描述|
|--|--|--|
|内存屏障|memory barriers|是一组处理器指令，用于实现对内存操作地顺序限制|
|缓冲行|cache line|缓存中可以分配的最小存储单位。处理器填写缓存线时会加载整个缓存线，需要使用多个主内存读周期|
|原子操作|atomic operations|不可中断的一个或一系列操作|
|缓存行填充|cache line fill|当处理器识别到从内存中读取操作时可缓存的，处理器读取整个缓存到适当的缓存(L1、L2、L3的或所有)|
|缓存命中|cache hit|如果进行告诉缓存行填充操作的内存仍然时下次处理器访问的地址时，处理器从缓存中读取操作数，而不是从内存中读取|
|写命中|write hit|当处理器及那个操作数写回到一个内存缓存的区域时，它首先会检查这个缓存的内存地址是否在缓存行中，如果存在一个有效的缓存行，则处理器将这个操作数写回到缓存中，而不是写回到内存，这个操作被称为写命中|
|写缺失|write misses th cache|一个有效的缓存行被写入到不存在的内存区域|

计算机在运行程序时，每条指令都是在CPU中完成的，在执行这些操作时，肯定需要跟内存打交道，考虑到效率的问题，CPU提出了缓存行的概念。因为读写主存中的数据肯定没有在CPU中执行速度快。那么如果所有的交互都是在跟主内存打交道，那么速率可想而知。为了高效的性能，CPU提出了缓存行填充的概念，当处理器识别到从内存中读取操作课缓存时，处理器会将读取的整个缓存到适合的缓存中。这样就大大的提高了效率，这是这种高速缓存是以CPU为单位的，并不能与其他CPU共享。CPU高速缓存为某个CPU独有的，只与该CPU运行的线程有关。

有了CPU高速缓存，它解决了读取的效率问题，可是带来了另外一个问题，就是数据的 一致性 的问题。当一个CPU将当前变量缓存到缓存行后，其中任何操作都不会跟主内存打交道，直到操作结束后，才会将最终的数据刷新到主内存中，那么在这途中，如果有其他的线程另外的CPU来读取并且缓存该数据，就会出现数据不一致的问题。

```java
int i = 0;

i++;
```

当第一个线程进入程序，并且读取i的值并且缓存到缓存行进行1次叠加操作，（理想值为1）；但是就在第一个线程还没操作完，第二个线程也进入程序操作该变量，那么由线程一修改的值并不会即使的刷新给线程二，所以在线程二操作完之后，i 的值还是为1。

此时java针对此问题提出了两种解决方法：

1. 通过总线锁的方式，实现数据的一致性。

2. 通过缓存一致性协议

下面会详细分析两种方式的实现方式与利弊。
- 总线锁：字面理解，就是在总线程上加上一把锁，次方式采用的一种独占的方式来实现的，即只能一个CPU能够运行，其他CPU都得阻塞，效率较为低下。

- 缓存一致性协议（MESI协议）：它确保了每个缓存中使用的共享变量的副本是一致的。在多核处理器系统中进行操作时，处理器会使用嗅探技术保证它的内部缓存、系统内存和其他处理器缓存的数据在总线上保持一致，当嗅探技术，方法总线上的数据，与缓存中的数据不一致时，那么正在嗅探的处理器会将它的缓存行无效，在下次访问相同内存地址时，强制执行缓存行填充。

# JVM的内存模型的三大特征
- 原子性

原子性 (Atomicity)：由Java内存模型来直接保证的原子性操作的read、load、assign、use、store和write。所谓的原子性就是再一个操作或者一系列操作中，要不全部完成，要么全部失败。（Java内存模型中提到我们大致可以认为基本数据类型的访问读写是具备原子性的。【其中例外long和double的非原子性协定。但这种情况几乎不会发生。】）

- 可见性

可见性 (Visibility)：可见性是指当一个线程在修改一个变量之后，其他的线程能够立即得到这个修改后的新值。在这里普通变量并不能实现这种所谓的可见性。而被volatile修饰过的变量则可是实现这种可见性。在变量修改时会将新的值第一时间同步到主内存中，那么其他有缓存该变量的线程通过嗅探和缓存一致性协定发现该线程缓存的值跟主内存的值并不一致时会将当前线程的缓存的值无效。在下次调用该变量时会访问主内存，实现线程之间的可见性。

除了volatile之外，Java还有两个关键字可以实现可见性，即synchronized和final。同步块的可见性是由“对一个变量施行unlock解锁之前，必须将修改完的变量写会到主内存中”。而final修饰的变量的可见性是指，一但变量被指定为final那么该变量只要没有将this的引用传递出去（这里涉及到的是this引用逃逸），那么在其他线程中就能看见该final字段的值。如下列代码所示：i与j都具备可见性。无须同步就能被其他线程正确访问。

- 有序性

有序性 (Oredering)：Java程序中天然的有序性可以总结为一句话：如果在本线程内观察，所有的操作都是有序的；如果在一个线程中观察另外一个线程，那么所有的操作都是无序的。

前半句：指的是线程内表现为串行语义（within-Thread As-If-Serial Semantics）。

后半句：“指令重排序”现象和“工作内存与主内存同步延迟”现象

其中volatile关键字本身就包含了禁止指令重排序的语义。

那么valatile是怎么保证可见性的呢？所以我们需要运用工具来查看JIT编译器生成的汇编指令查看volatile进行写操作时，CPU会做什么事情。

通过以下代码，在使用JIT编译工具来查看volatile的真正面目：

```
-XX:+UnlockDiagnosticVMOptions

-XX:+PrintAssembly

-XX:+LogCompilation

-XX:LogFile=jit.log
```

在对使用volatile修饰的变量v1进行写操作时，会多出一个前缀为lock的指令，该指令会在多核处理器下回引发两件事情：

1. 将当前处理器缓存行的数据写会到系统内存中。

2. 这个写回内存的操作会使在其他CPU里缓存了该内存地址的数据无效（CPU的嗅探，来保证数据的一致性）。

# 总结
volatile看起来非常的简单，并且轻巧。但是也存在很多弊端。相较于synchronized来说，在某些场景可以代替synchronized，但又不能完全取代。因为在使用volatile时，大部分只能修饰关键变量，并且如果大量使用volatile反而会影响运行效率。因为volatile会禁止指令重排序，禁用CPU优化。在使用它必须满足如下两个条件：

1. 对变量的写操作不依赖当前值；

2. 该变量没有包含在具有其他变量的不变式中；

```
volatile经常用于两个场景：状态标记量、double check
```
