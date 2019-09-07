# 使用 ForkJoinPool 进行分叉和合并
ForkJoinPool 和 ExecutorService 很相似，除了一点不同。ForkJoinPool 让我们可以很方便地把任务分裂成几个更小的任务，这些分裂出来的任务也将会提交给 ForkJoinPool。任务可以继续分割成更小的子任务，只要它还能分割。可能听起来有些抽象，因此本节中我们将会解释 ForkJoinPool 是如何工作的，还有任务分割是如何进行的。

## 分叉
一个使用了分叉和合并原理的任务可以将自己分叉(分割)为更小的子任务，这些子任务可以被并发执行。

通过把自己分割成多个子任务，每个子任务可以由不同的 CPU 并行执行，或者被同一个 CPU 上的不同线程执行。

只有当给的任务过大，把它分割成几个子任务才有意义。把任务分割成子任务有一定开销，因此对于小型任务，这个分割的消耗可能比每个子任务并发执行的消耗还要大。

什么时候把一个任务分割成子任务是有意义的，这个界限也称作一个阀值。这要看每个任务对有意义阀值的决定。很大程度上取决于它要做的工作的种类。

## 合并
当一个任务将自己分割成若干子任务之后，该任务将进入等待所有子任务的结束之中。

一旦子任务执行结束，该任务可以把所有结果合并到同一个结果。

当然，并非所有类型的任务都会返回一个结果。如果这个任务并不返回一个结果，它只需等待所有子任务执行完毕。也就不需要结果的合并啦。

## ForkJoinPool
ForkJoinPool 是一个特殊的线程池，它的设计是为了更好的配合 分叉-和-合并 任务分割的工作。ForkJoinPool 也在 java.util.concurrent 包中，其完整类名为 java.util.concurrent.ForkJoinPool。

## 创建一个 ForkJoinPool
你可以通过其构造子创建一个 ForkJoinPool。作为传递给 ForkJoinPool 构造子的一个参数，你可以定义你期望的并行级别。并行级别表示你想要传递给 ForkJoinPool 的任务所需的线程或 CPU 数量。以下是一个 ForkJoinPool 示例：

```java
ForkJoinPool forkJoinPool = new ForkJoinPool(4);  
```

这个示例创建了一个并行级别为 4 的 ForkJoinPool。

## 提交任务到 ForkJoinPool
就像提交任务到 ExecutorService 那样，把任务提交到 ForkJoinPool。你可以提交两种类型的任务。一种是没有任何返回值的(一个 “行动”)，另一种是有返回值的(一个”任务”)。这两种类型分别由 RecursiveAction 和 RecursiveTask 表示。接下来介绍如何使用这两种类型的任务，以及如何对它们进行提交。

## RecursiveAction
RecursiveAction 是一种没有任何返回值的任务。它只是做一些工作，比如写数据到磁盘，然后就退出了。 

一个 RecursiveAction 可以把自己的工作分割成更小的几块，这样它们可以由独立的线程或者 CPU 执行。 

你可以通过继承来实现一个 RecursiveAction。示例如下：

```java
import java.util.ArrayList;  
import java.util.List;  
import java.util.concurrent.RecursiveAction;  
 
public class MyRecursiveAction extends RecursiveAction {  
 
    private long workLoad = 0;  
 
    public MyRecursiveAction(long workLoad) {  
        this.workLoad = workLoad;  
    }  
 
    @Override  
    protected void compute() {  
 
        //if work is above threshold, break tasks up into smaller tasks  
        if(this.workLoad > 16) {  
            System.out.println("Splitting workLoad : " + this.workLoad);  
 
            List<MyRecursiveAction> subtasks =  
                new ArrayList<MyRecursiveAction>();  
 
            subtasks.addAll(createSubtasks());  
 
            for(RecursiveAction subtask : subtasks){  
                subtask.fork();  
            }  
 
        } else {  
            System.out.println("Doing workLoad myself: " + this.workLoad);  
        }  
    }  
 
    private List<MyRecursiveAction> createSubtasks() {  
        List<MyRecursiveAction> subtasks =  
            new ArrayList<MyRecursiveAction>();  
 
        MyRecursiveAction subtask1 = new MyRecursiveAction(this.workLoad / 2);  
        MyRecursiveAction subtask2 = new MyRecursiveAction(this.workLoad / 2);  
 
        subtasks.add(subtask1);  
        subtasks.add(subtask2);  
 
        return subtasks;  
    }  
 
}  
```

例子很简单。MyRecursiveAction 将一个虚构的 workLoad 作为参数传给自己的构造子。如果 workLoad 高于一个特定阀值，该工作将被分割为几个子工作，子工作继续分割。如果 workLoad 低于特定阀值，该工作将由 MyRecursiveAction 自己执行。

你可以这样规划一个 MyRecursiveAction 的执行：

```java
MyRecursiveAction myRecursiveAction = new MyRecursiveAction(24);  
 
forkJoinPool.invoke(myRecursiveAction);  
```

## RecursiveTask
RecursiveTask 是一种会返回结果的任务。它可以将自己的工作分割为若干更小任务，并将这些子任务的执行结果合并到一个集体结果。可以有几个水平的分割和合并。以下是一个 RecursiveTask 示例：

```java
import java.util.ArrayList;  
import java.util.List;  
import java.util.concurrent.RecursiveTask;  
 
 
public class MyRecursiveTask extends RecursiveTask<Long> {  
 
    private long workLoad = 0;  
 
    public MyRecursiveTask(long workLoad) {  
        this.workLoad = workLoad;  
    }  
 
    protected Long compute() {  
 
        //if work is above threshold, break tasks up into smaller tasks  
        if(this.workLoad > 16) {  
            System.out.println("Splitting workLoad : " + this.workLoad);  
 
            List<MyRecursiveTask> subtasks =  
                new ArrayList<MyRecursiveTask>();  
            subtasks.addAll(createSubtasks());  
 
            for(MyRecursiveTask subtask : subtasks){  
                subtask.fork();  
            }  
 
            long result = 0;  
            for(MyRecursiveTask subtask : subtasks) {  
                result += subtask.join();  
            }  
            return result;  
 
        } else {  
            System.out.println("Doing workLoad myself: " + this.workLoad);  
            return workLoad * 3;  
        }  
    }  
 
    private List<MyRecursiveTask> createSubtasks() {  
        List<MyRecursiveTask> subtasks =  
        new ArrayList<MyRecursiveTask>();  
 
        MyRecursiveTask subtask1 = new MyRecursiveTask(this.workLoad / 2);  
        MyRecursiveTask subtask2 = new MyRecursiveTask(this.workLoad / 2);  
 
        subtasks.add(subtask1);  
        subtasks.add(subtask2);  
 
        return subtasks;  
    }  
}  
```

除了有一个结果返回之外，这个示例和 RecursiveAction 的例子很像。MyRecursiveTask 类继承自 RecursiveTask，这也就意味着它将返回一个 Long 类型的结果。

MyRecursiveTask 示例也会将工作分割为子任务，并通过 fork() 方法对这些子任务计划执行。

此外，本示例还通过调用每个子任务的 join() 方法收集它们返回的结果。子任务的结果随后被合并到一个更大的结果，并最终将其返回。对于不同级别的递归，这种子任务的结果合并可能会发生递归。

你可以这样规划一个 RecursiveTask：

```java
MyRecursiveTask myRecursiveTask = new MyRecursiveTask(128);  
 
long mergedResult = forkJoinPool.invoke(myRecursiveTask);  
 
System.out.println("mergedResult = " + mergedResult);   
```

注意是如何通过 ForkJoinPool.invoke() 方法的调用来获取最终执行结果的。
