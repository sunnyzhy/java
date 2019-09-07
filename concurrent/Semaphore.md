# 信号量 Semaphore
java.util.concurrent.Semaphore 类是一个计数信号量。这就意味着它具备两个主要方法：
- acquire()
- release()

计数信号量由一个指定数量的 “许可” 初始化。每调用一次 acquire()，一个许可会被调用线程取走。每调用一次 release()，一个许可会被返还给信号量。因此，在没有任何 release() 调用时，最多有 N 个线程能够通过 acquire() 方法，N 是该信号量初始化时的许可的指定数量。

## Semaphore 用法
信号量主要有两种用途：
- 保护一个重要(代码)部分防止一次超过 N 个线程进入
- 在两个线程之间发送信号

## 保护重要部分
如果你将信号量用于保护一个重要部分，试图进入这一部分的代码通常会首先尝试获得一个许可，然后才能进入重要部分(代码块)，执行完之后，再把许可释放掉。比如这样：

```java
Semaphore semaphore = new Semaphore(1);  
 
//critical section  
semaphore.acquire();  
 
...  
 
semaphore.release();  
```

## 在线程之间发送信号
如果你将一个信号量用于在两个线程之间传送信号，通常你应该用一个线程调用 acquire() 方法，而另一个线程调用 release() 方法。

如果没有可用的许可，acquire() 调用将会阻塞，直到一个许可被另一个线程释放出来。同理，如果无法往信号量释放更多许可时，一个 release() 调用也会阻塞。

通过这个可以对多个线程进行协调。比如，如果线程 1 将一个对象插入到了一个共享列表(list)之后之后调用了 acquire()，而线程 2 则在从该列表中获取一个对象之前调用了 release()，这时你其实已经创建了一个阻塞队列。信号量中可用的许可的数量也就等同于该阻塞队列能够持有的元素个数。

## 公平
没有办法保证线程能够公平地可从信号量中获得许可。也就是说，无法担保掉第一个调用 acquire() 的线程会是第一个获得一个许可的线程。如果第一个线程在等待一个许可时发生阻塞，而第二个线程前来索要一个许可的时候刚好有一个许可被释放出来，那么它就可能会在第一个线程之前获得许可。 
如果你想要强制公平，Semaphore 类有一个具有一个布尔类型的参数的构造子，通过这个参数以告知 Semaphore 是否要强制公平。强制公平会影响到并发性能，所以除非你确实需要它否则不要启用它。

以下是如何在公平模式创建一个 Semaphore 的示例：

```java
Semaphore semaphore = new Semaphore(1, true);  
```

更多方法
java.util.concurrent.Semaphore 类还有很多方法，比如：
- availablePermits()
- acquireUninterruptibly()
- drainPermits()
- hasQueuedThreads()
- getQueuedThreads()
- tryAcquire()
