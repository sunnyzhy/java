# 执行器服务 ExecutorService
java.util.concurrent.ExecutorService 接口表示一个异步执行机制，使我们能够在后台执行任务。因此一个 ExecutorService 很类似于一个线程池。实际上，存在于 java.util.concurrent 包里的 ExecutorService 实现就是一个线程池实现。

## ExecutorService 例子
以下是一个简单的 ExecutorService 例子：

```java
ExecutorService executorService = Executors.newFixedThreadPool(10);  
 
executorService.execute(new Runnable() {  
    public void run() {  
        System.out.println("Asynchronous task");  
    }  
});  
 
executorService.shutdown();  
```

首先使用 newFixedThreadPool() 工厂方法创建一个 ExecutorService。这里创建了一个十个线程执行任务的线程池。 

然后，将一个 Runnable 接口的匿名实现类传递给 execute() 方法。这将导致 ExecutorService 中的某个线程执行该 Runnable。

## 任务委派
一个线程将一个任务委派给一个 ExecutorService 去异步执行。 

一旦该线程将任务委派给 ExecutorService，该线程将继续它自己的执行，独立于该任务的执行。

## ExecutorService 实现
既然 ExecutorService 是个接口，如果你想用它的话就得去使用它的实现类之一。java.util.concurrent 包提供了 ExecutorService 接口的以下实现类：
- ThreadPoolExecutor
- ScheduledThreadPoolExecutor

## 创建一个 ExecutorService
ExecutorService 的创建依赖于你使用的具体实现。但是你也可以使用 Executors 工厂类来创建 ExecutorService 实例。以下是几个创建 ExecutorService 实例的例子：

```java
ExecutorService executorService1 = Executors.newSingleThreadExecutor();  
ExecutorService executorService2 = Executors.newFixedThreadPool(10);  
ExecutorService executorService3 = Executors.newScheduledThreadPool(10);  
```

## ExecutorService 使用
有几种不同的方式来将任务委托给 ExecutorService 去执行：
- execute(Runnable)
- submit(Runnable)
- submit(Callable)
- invokeAny(…)
- invokeAll(…)

接下来我们挨个看一下这些方法:

### execute(Runnable)
execute(Runnable) 方法要求一个 java.lang.Runnable 对象，然后对它进行异步执行。以下是使用 ExecutorService 执行一个 Runnable 的示例：

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();  
 
executorService.execute(new Runnable() {  
    public void run() {  
        System.out.println("Asynchronous task");  
    }  
});  
 
executorService.shutdown();  
```

没有办法得知被执行的 Runnable 的执行结果。如果有需要的话你得使用一个 Callable。

### submit(Runnable)
submit(Runnable) 方法也要求一个 Runnable 实现类，但它返回一个 Future 对象。这个 Future 对象可以用来检查 Runnable 是否已经执行完毕。

以下是 ExecutorService submit() 示例：

```java
Future future = executorService.submit(new Runnable() {  
    public void run() {  
        System.out.println("Asynchronous task");  
    }  
});  
 
future.get();  //returns null if the task has finished correctly. 
```

### submit(Callable)
submit(Callable) 方法类似于 submit(Runnable) 方法，除了它所要求的参数类型之外。Callable 实例除了它的 call() 方法能够返回一个结果之外和一个 Runnable 很相像。Runnable.run() 不能够返回一个结果。 
Callable 的结果可以通过 submit(Callable) 方法返回的 Future 对象进行获取。以下是一个 ExecutorService Callable 示例：

```java
Future future = executorService.submit(new Callable(){  
    public Object call() throws Exception {  
        System.out.println("Asynchronous Callable");  
        return "Callable Result";  
    }  
});  
 
System.out.println("future.get() = " + future.get());  
```

以上代码输出：

```
Asynchronous Callable
future.get() = Callable Result
```

### invokeAny()
invokeAny() 方法要求一系列的 Callable 或者其子接口的实例对象。调用这个方法并不会返回一个 Future，但它返回其中一个 Callable 对象的结果。无法保证返回的是哪个 Callable 的结果 - 只能表明其中一个已执行结束。

如果其中一个任务执行结束(或者抛了一个异常)，其他 Callable 将被取消。

以下是示例代码：

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();  
 
Set<Callable<String>> callables = new HashSet<Callable<String>>();  
 
callables.add(new Callable<String>() {  
    public String call() throws Exception {  
        return "Task 1";  
    }  
});  
callables.add(new Callable<String>() {  
    public String call() throws Exception {  
        return "Task 2";  
    }  
});  
callables.add(new Callable<String>() {  
    public String call() throws Exception {  
        return "Task 3";  
    }  
});  
 
String result = executorService.invokeAny(callables);  
 
System.out.println("result = " + result);  
 
executorService.shutdown();  
```

上述代码将会打印出给定 Callable 集合中的一个的执行结果。我自己试着执行了它几次，结果始终在变。有时是 “Task 1”，有时是 “Task 2” 等等。

### invokeAll()
invokeAll() 方法将调用你在集合中传给 ExecutorService 的所有 Callable 对象。invokeAll() 返回一系列的 Future 对象，通过它们你可以获取每个 Callable 的执行结果。

记住，一个任务可能会由于一个异常而结束，因此它可能没有 “成功”。无法通过一个 Future 对象来告知我们是两种结束中的哪一种。

以下是一个代码示例：

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();  
 
Set<Callable<String>> callables = new HashSet<Callable<String>>();  
 
callables.add(new Callable<String>() {  
    public String call() throws Exception {  
        return "Task 1";  
    }  
});  
callables.add(new Callable<String>() {  
    public String call() throws Exception {  
        return "Task 2";  
    }  
});  
callables.add(new Callable<String>() {  
    public String call() throws Exception {  
        return "Task 3";  
    }  
});  
 
List<Future<String>> futures = executorService.invokeAll(callables);  
 
for(Future<String> future : futures){  
    System.out.println("future.get = " + future.get());  
}  
 
executorService.shutdown();  
```

## ExecutorService 关闭
使用完 ExecutorService 之后你应该将其关闭，以使其中的线程不再运行。

比如，如果你的应用是通过一个 main() 方法启动的，之后 main 方法退出了你的应用，如果你的应用有一个活动的 ExexutorService 它将还会保持运行。ExecutorService 里的活动线程阻止了 JVM 的关闭。

要终止 ExecutorService 里的线程你需要调用 ExecutorService 的 shutdown() 方法。ExecutorService 并不会立即关闭，但它将不再接受新的任务，而且一旦所有线程都完成了当前任务的时候，ExecutorService 将会关闭。在 shutdown() 被调用之前所有提交给 ExecutorService 的任务都被执行。

如果你想要立即关闭 ExecutorService，你可以调用 shutdownNow() 方法。这样会立即尝试停止所有执行中的任务，并忽略掉那些已提交但尚未开始处理的任务。无法担保执行任务的正确执行。可能它们被停止了，也可能已经执行结束。
