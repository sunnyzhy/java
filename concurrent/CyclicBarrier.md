# 栅栏 CyclicBarrier
 
java.util.concurrent.CyclicBarrier 类是一种同步机制，它能够对处理一些算法的线程实现同步。换句话讲，它就是一个所有线程必须等待的一个栅栏，直到所有线程都到达这里，然后所有线程才可以继续做其他事情。

通过调用 CyclicBarrier 对象的 await() 方法，两个线程可以实现互相等待。一旦 N 个线程在等待 CyclicBarrier 达成，所有线程将被释放掉去继续运行。

## 创建一个 CyclicBarrier
在创建一个 CyclicBarrier 的时候你需要定义有多少线程在被释放之前等待栅栏。创建 CyclicBarrier 示例：

```java
CyclicBarrier barrier = new CyclicBarrier(2);  
```

## 等待一个 CyclicBarrier
以下演示了如何让一个线程等待一个 CyclicBarrier：

```java
barrier.await();  
```

当然，你也可以为等待线程设定一个超时时间。等待超过了超时时间之后，即便还没有达成 N 个线程等待 CyclicBarrier 的条件，该线程也会被释放出来。以下是定义超时时间示例：

```java
barrier.await(10, TimeUnit.SECONDS);  
```

满足以下任何条件都可以让等待 CyclicBarrier 的线程释放：
- 最后一个线程也到达 CyclicBarrier(调用 await())
- 当前线程被其他线程打断(其他线程调用了这个线程的 interrupt() 方法)
- 其他等待栅栏的线程被打断
- 其他等待栅栏的线程因超时而被释放
- 外部线程调用了栅栏的 CyclicBarrier.reset() 方法

## CyclicBarrier 行动
CyclicBarrier 支持一个栅栏行动，栅栏行动是一个 Runnable 实例，一旦最后等待栅栏的线程抵达，该实例将被执行。你可以在 CyclicBarrier 的构造方法中将 Runnable 栅栏行动传给它：

```java
Runnable barrierAction = ... ;  
CyclicBarrier barrier = new CyclicBarrier(2, barrierAction);  
```

## CyclicBarrier 示例
以下代码演示了如何使用 CyclicBarrier：

```java
Runnable barrier1Action = new Runnable() {  
    public void run() {  
        System.out.println("BarrierAction 1 executed ");  
    }  
};  
Runnable barrier2Action = new Runnable() {  
    public void run() {  
        System.out.println("BarrierAction 2 executed ");  
    }  
};  
 
CyclicBarrier barrier1 = new CyclicBarrier(2, barrier1Action);  
CyclicBarrier barrier2 = new CyclicBarrier(2, barrier2Action);  
 
CyclicBarrierRunnable barrierRunnable1 = new CyclicBarrierRunnable(barrier1, barrier2);  
 
CyclicBarrierRunnable barrierRunnable2 = new CyclicBarrierRunnable(barrier1, barrier2);  
 
new Thread(barrierRunnable1).start();  
new Thread(barrierRunnable2).start();  
```

CyclicBarrierRunnable 类：

```java
public class CyclicBarrierRunnable implements Runnable{  
 
    CyclicBarrier barrier1 = null;  
    CyclicBarrier barrier2 = null;  
 
    public CyclicBarrierRunnable(  
            CyclicBarrier barrier1,  
            CyclicBarrier barrier2) {  
 
        this.barrier1 = barrier1;  
        this.barrier2 = barrier2;  
    }  
 
    public void run() {  
        try {  
            Thread.sleep(1000);  
            System.out.println(Thread.currentThread().getName() +  
                                " waiting at barrier 1");  
            this.barrier1.await();  
 
            Thread.sleep(1000);  
            System.out.println(Thread.currentThread().getName() +  
                                " waiting at barrier 2");  
            this.barrier2.await();  
 
            System.out.println(Thread.currentThread().getName() +  
                                " done!");  
 
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        } catch (BrokenBarrierException e) {  
            e.printStackTrace();  
        }  
    }  
}  
```

以上代码控制台输出如下。注意每个线程写入控制台的时序可能会跟你实际执行不一样。比如有时 Thread-0 先打印，有时 Thread-1 先打印。

```
Thread-0 waiting at barrier 1
Thread-1 waiting at barrier 1
BarrierAction 1 executed
Thread-1 waiting at barrier 2
Thread-0 waiting at barrier 2
BarrierAction 2 executed
Thread-0 done!
Thread-1 done!
```
