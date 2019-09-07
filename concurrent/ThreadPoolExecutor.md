# 线程池执行者 ThreadPoolExecutor
java.util.concurrent.ThreadPoolExecutor 是 ExecutorService 接口的一个实现。ThreadPoolExecutor 使用其内部池中的线程执行给定任务(Callable 或者 Runnable)。

ThreadPoolExecutor 包含的线程池能够包含不同数量的线程。池中线程的数量由以下变量决定：
- corePoolSize
- maximumPoolSize

当一个任务委托给线程池时，如果池中线程数量低于 corePoolSize，一个新的线程将被创建，即使池中可能尚有空闲线程。 

如果内部任务队列已满，而且有至少 corePoolSize 正在运行，但是运行线程的数量低于 maximumPoolSize，一个新的线程将被创建去执行该任务。 

## 创建一个 ThreadPoolExecutor
ThreadPoolExecutor 有若干个可用构造子。比如：

```java
int corePoolSize = 5;  
int maxPoolSize = 10;  
long keepAliveTime = 5000;  
 
ExecutorService threadPoolExecutor =  
        new ThreadPoolExecutor(  
                corePoolSize,  
                maxPoolSize,  
                keepAliveTime,  
                TimeUnit.MILLISECONDS,  
                new LinkedBlockingQueue<Runnable>()  
                );  
```

但是，除非你确实需要显式为 ThreadPoolExecutor 定义所有参数，使用 java.util.concurrent.Executors 类中的工厂方法之一会更加方便，正如 ExecutorService 小节所述。
