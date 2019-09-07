# 定时执行者服务 ScheduledExecutorService
java.util.concurrent.ScheduledExecutorService 是一个 ExecutorService， 它能够将任务延后执行，或者间隔固定时间多次执行。 任务由一个工作者线程异步执行，而不是由提交任务给 ScheduledExecutorService 的那个线程执行。

## ScheduledExecutorService 例子
以下是一个简单的 ScheduledExecutorService 示例：

```java
ScheduledExecutorService scheduledExecutorService =  
        Executors.newScheduledThreadPool(5);  
 
ScheduledFuture scheduledFuture =  
    scheduledExecutorService.schedule(new Callable() {  
        public Object call() throws Exception {  
            System.out.println("Executed!");  
            return "Called!";  
        }  
    },  
    5,  
    TimeUnit.SECONDS);  
```

首先一个内置 5 个线程的 ScheduledExecutorService 被创建。之后一个 Callable 接口的匿名类示例被创建然后传递给 schedule() 方法。后边的俩参数定义了 Callable 将在 5 秒钟之后被执行。

## ScheduledExecutorService 实现
既然 ScheduledExecutorService 是一个接口，你要用它的话就得使用 java.util.concurrent 包里对它的某个实现类。ScheduledExecutorService 具有以下实现类：ScheduledThreadPoolExecutor

## 创建一个 ScheduledExecutorService
如何创建一个 ScheduledExecutorService 取决于你采用的它的实现类。但是你也可以使用 Executors 工厂类来创建一个 ScheduledExecutorService 实例。比如：

```java
ScheduledExecutorService scheduledExecutorService =  Executors.newScheduledThreadPool(5);  
```

## ScheduledExecutorService 使用
一旦你创建了一个 ScheduledExecutorService，你可以通过调用它的以下方法：
- schedule (Callable task, long delay, TimeUnit timeunit)
- schedule (Runnable task, long delay, TimeUnit timeunit)
- scheduleAtFixedRate (Runnable, long initialDelay, long period, TimeUnit timeunit)
- scheduleWithFixedDelay (Runnable, long initialDelay, long period, TimeUnit timeunit)

下面我们就简单看一下这些方法:

### schedule (Callable task, long delay, TimeUnit timeunit)
这个方法计划指定的 Callable 在给定的延迟之后执行。 

这个方法返回一个 ScheduledFuture，通过它你可以在它被执行之前对它进行取消，或者在它执行之后获取结果。 

以下是一个示例：

```java
ScheduledExecutorService scheduledExecutorService =  
        Executors.newScheduledThreadPool(5);  
 
ScheduledFuture scheduledFuture =  
    scheduledExecutorService.schedule(new Callable() {  
        public Object call() throws Exception {  
            System.out.println("Executed!");  
            return "Called!";  
        }  
    },  
    5,  
    TimeUnit.SECONDS);  
 
System.out.println("result = " + scheduledFuture.get());  
 
scheduledExecutorService.shutdown();  
```

示例输出结果：

```
Executed!
result = Called!
```

### schedule (Runnable task, long delay, TimeUnit timeunit)
除了 Runnable 无法返回一个结果之外，这一方法工作起来就像以一个 Callable 作为一个参数的那个版本的方法一样，因此 ScheduledFuture.get() 在任务执行结束之后返回 null。

### scheduleAtFixedRate (Runnable, long initialDelay, long period, TimeUnit timeunit)
这一方法规划一个任务将被定期执行。该任务将会在首个 initialDelay 之后得到执行，然后每个 period 时间之后重复执行。

如果给定任务的执行抛出了异常，该任务将不再执行。如果没有任何异常的话，这个任务将会持续循环执行到 ScheduledExecutorService 被关闭。 
如果一个任务占用了比计划的时间间隔更长的时候，下一次执行将在当前执行结束执行才开始。计划任务在同一时间不会有多个线程同时执行。

### scheduleWithFixedDelay (Runnable, long initialDelay, long period, TimeUnit timeunit)
除了 period 有不同的解释之外这个方法和 scheduleAtFixedRate() 非常像。

scheduleAtFixedRate() 方法中，period 被解释为前一个执行的开始和下一个执行的开始之间的间隔时间。

而在本方法中，period 则被解释为前一个执行的结束和下一个执行的结束之间的间隔。因此这个延迟是执行结束之间的间隔，而不是执行开始之间的间隔。

## ScheduledExecutorService 关闭
正如 ExecutorService，在你使用结束之后你需要把 ScheduledExecutorService 关闭掉。否则他将导致 JVM 继续运行，即使所有其他线程已经全被关闭。

你可以使用从 ExecutorService 接口继承来的 shutdown() 或 shutdownNow() 方法将 ScheduledExecutorService 关闭。参见 ExecutorService 关闭部分以获取更多信息。
