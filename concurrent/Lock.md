# 锁Lock
java.util.concurrent.locks.Lock 是一个类似于 synchronized 块的线程同步机制。但是 Lock 比 synchronized 块更加灵活、精细。 

## Java Lock 例子
既然 Lock 是一个接口，在你的程序里需要使用它的实现类之一来使用它。以下是一个简单示例：

```java
Lock lock = new ReentrantLock();  
 
lock.lock();  
 
//critical section  
 
lock.unlock();  
```

首先创建了一个 Lock 对象。之后调用了它的 lock() 方法。这时候这个 lock 实例就被锁住啦。任何其他再过来调用 lock() 方法的线程将会被阻塞住，直到锁定 lock 实例的线程调用了 unlock() 方法。最后 unlock() 被调用了，lock 对象解锁了，其他线程可以对它进行锁定了。

## Java Lock 实现
java.util.concurrent.locks 包提供了以下对 Lock 接口的实现类：ReentrantLock

## Lock 和 synchronized 代码块的主要不同点
一个 Lock 对象和一个 synchronized 代码块之间的主要不同点是：
- synchronized 代码块不能够保证进入访问等待的线程的先后顺序。
- 你不能够传递任何参数给一个 synchronized 代码块的入口。因此，对于 synchronized 代码块的访问等待设置超时时间是不可能的事情。
- synchronized 块必须被完整地包含在单个方法里。而一个 Lock 对象可以把它的 lock() 和 unlock() 方法的调用放在不同的方法里。

## Lock 的方法
Lock 接口具有以下主要方法：

- lock()

lock() 将 Lock 实例锁定。如果该 Lock 实例已被锁定，调用 lock() 方法的线程将会阻塞，直到 Lock 实例解锁。

- lockInterruptibly()

lockInterruptibly() 方法将会被调用线程锁定，除非该线程被打断。此外，如果一个线程在通过这个方法来锁定 Lock 对象时进入阻塞等待，而它被打断了的话，该线程将会退出这个方法调用。

- tryLock()

tryLock() 方法试图立即锁定 Lock 实例。如果锁定成功，它将返回 true，如果 Lock 实例已被锁定该方法返回 false。这一方法永不阻塞。

- tryLock(long timeout, TimeUnit timeUnit)

tryLock(long timeout, TimeUnit timeUnit) 的工作类似于 tryLock() 方法，除了它在放弃锁定 Lock 之前等待一个给定的超时时间之外。

- unlock()

unlock() 方法对 Lock 实例解锁。一个 Lock 实现将只允许锁定了该对象的线程来调用此方法。其他(没有锁定该 Lock 对象的线程)线程对 unlock() 方法的调用将会抛一个未检查异常(RuntimeException)。

# 读写锁 ReadWriteLock
java.util.concurrent.locks.ReadWriteLock 读写锁是一种先进的线程锁机制。它能够允许多个线程在同一时间对某特定资源进行读取，但同一时间内只能有一个线程对其进行写入。

读写锁的理念在于多个线程能够对一个共享资源进行读取，而不会导致并发问题。并发问题的发生场景在于对一个共享资源的读和写操作的同时进行，或者多个写操作并发进行。

ReadWriteLock 锁规则
一个线程在对受保护资源在读或者写之前对 ReadWriteLock 锁定的规则如下： 
- 读锁：如果没有任何写操作线程锁定 ReadWriteLock，并且没有任何写操作线程要求一个写锁(但还没有获得该锁)。因此，可以有多个读操作线程对该锁进行锁定。

- 写锁：如果没有任何读操作或者写操作。因此，在写操作的时候，只能有一个线程对该锁进行锁定。

## ReadWriteLock 实现
ReadWriteLock 是个接口，如果你想用它的话就得去使用它的实现类之一。java.util.concurrent.locks 包提供了 ReadWriteLock 接口的以下实现类：ReentrantReadWriteLock

## ReadWriteLock 代码示例
以下是 ReadWriteLock 的创建以及如何使用它进行读、写锁定的简单示例代码：

```java
ReadWriteLock readWriteLock = new ReentrantReadWriteLock();  
 
readWriteLock.readLock().lock();  
 
// multiple readers can enter this section  
// if not locked for writing, and not writers waiting  
// to lock for writing.  
 
readWriteLock.readLock().unlock();  
 
readWriteLock.writeLock().lock();  
 
// only one writer can enter this section,  
// and only if no threads are currently reading.  
 
readWriteLock.writeLock().unlock();  
```

注意如何使用 ReadWriteLock 对两种锁实例的持有。一个对读访问进行保护，一个队写访问进行保护。

# 原子性布尔 AtomicBoolean
AtomicBoolean 类为我们提供了一个可以用原子方式进行读和写的布尔值，它还拥有一些先进的原子性操作，比如 compareAndSet()。AtomicBoolean 类位于 java.util.concurrent.atomic 包，完整类名是为 java.util.concurrent.atomic.AtomicBoolean。

## 创建一个 AtomicBoolean
你可以这样创建一个 AtomicBoolean：

```java
AtomicBoolean atomicBoolean = new AtomicBoolean();  
```

以上示例新建了一个默认值为 false 的 AtomicBoolean。

如果你想要为 AtomicBoolean 实例设置一个显式的初始值，那么你可以将初始值传给 AtomicBoolean 的构造子：

```java
AtomicBoolean atomicBoolean = new AtomicBoolean(true);  
```

## 获取 AtomicBoolean 的值
你可以通过使用 get() 方法来获取一个 AtomicBoolean 的值。示例如下：

```java
AtomicBoolean atomicBoolean = new AtomicBoolean(true);  
 
boolean value = atomicBoolean.get();  
```

以上代码执行后 value 变量的值将为 true。

## 设置 AtomicBoolean 的值
你可以通过使用 set() 方法来设置一个 AtomicBoolean 的值。示例如下：

```java
AtomicBoolean atomicBoolean = new AtomicBoolean(true);  
 
atomicBoolean.set(false);  
```

以上代码执行后 AtomicBoolean 的值为 false。

## 交换 AtomicBoolean 的值
你可以通过 getAndSet() 方法来交换一个 AtomicBoolean 实例的值。getAndSet() 方法将返回 AtomicBoolean 当前的值，并将为 AtomicBoolean 设置一个新值。示例如下：

```java
AtomicBoolean atomicBoolean = new AtomicBoolean(true);  
boolean oldValue = atomicBoolean.getAndSet(false); 
```

以上代码执行后 oldValue 变量的值为 true，atomicBoolean 实例将持有 false 值。代码成功将 AtomicBoolean 当前值 ture 交换为 false。

## 比较并设置 AtomicBoolean 的值
compareAndSet() 方法允许你对 AtomicBoolean 的当前值与一个期望值进行比较，如果当前值等于期望值的话，将会对 AtomicBoolean 设定一个新值。compareAndSet() 方法是原子性的，因此在同一时间之内有单个线程执行它。因此 compareAndSet() 方法可被用于一些类似于锁的同步的简单实现。

以下是一个 compareAndSet() 示例：

```java
AtomicBoolean atomicBoolean = new AtomicBoolean(true);  
 
boolean expectedValue = true;  
boolean newValue      = false;  
 
boolean wasNewValueSet = atomicBoolean.compareAndSet(expectedValue, newValue);  
```

本示例对 AtomicBoolean 的当前值与 true 值进行比较，如果相等，将 AtomicBoolean 的值更新为 false。

# 原子性整型 AtomicInteger
AtomicInteger 类为我们提供了一个可以进行原子性读和写操作的 int 变量，它还包含一系列先进的原子性操作，比如 compareAndSet()。AtomicInteger 类位于 java.util.concurrent.atomic 包，因此其完整类名为 java.util.concurrent.atomic.AtomicInteger。

## 创建一个 AtomicInteger
创建一个 AtomicInteger 示例如下：

```java
AtomicInteger atomicInteger = new AtomicInteger();  
```

本示例将创建一个初始值为 0 的 AtomicInteger。 

如果你想要创建一个给定初始值的 AtomicInteger，你可以这样：

```java
AtomicInteger atomicInteger = new AtomicInteger(123);  
```

本示例将 123 作为参数传给 AtomicInteger 的构造子，它将设置 AtomicInteger 实例的初始值为 123。

## 获取 AtomicInteger 的值
你可以使用 get() 方法获取 AtomicInteger 实例的值。示例如下：

```java
AtomicInteger atomicInteger = new AtomicInteger(123);  
int theValue = atomicInteger.get();  
```

## 设置 AtomicInteger 的值
你可以通过 set() 方法对 AtomicInteger 的值进行重新设置。以下是 AtomicInteger.set() 示例：

```java
AtomicInteger atomicInteger = new AtomicInteger(123);  
atomicInteger.set(234);  
```

以上示例创建了一个初始值为 123 的 AtomicInteger，而在第二行将其值更新为 234。

## 比较并设置 AtomicInteger 的值
AtomicInteger 类也通过了一个原子性的 compareAndSet() 方法。这一方法将 AtomicInteger 实例的当前值与期望值进行比较，如果二者相等，为 AtomicInteger 实例设置一个新值。AtomicInteger.compareAndSet() 代码示例：

```java
AtomicInteger atomicInteger = new AtomicInteger(123);  
 
int expectedValue = 123;  
int newValue      = 234;  
atomicInteger.compareAndSet(expectedValue, newValue);  
```

本示例首先新建一个初始值为 123 的 AtomicInteger 实例。然后将 AtomicInteger 与期望值 123 进行比较，如果相等，将 AtomicInteger 的值更新为 234。

## 增加 AtomicInteger 值
AtomicInteger 类包含有一些方法，通过它们你可以增加 AtomicInteger 的值，并获取其值。这些方法如下：
- addAndGet()
- getAndAdd()
- getAndIncrement()
- incrementAndGet()

第一个 addAndGet() 方法给 AtomicInteger 增加了一个值，然后返回增加后的值。getAndAdd() 方法为 AtomicInteger 增加了一个值，但返回的是增加以前的 AtomicInteger 的值。具体使用哪一个取决于你的应用场景。以下是这两种方法的示例：

```java
AtomicInteger atomicInteger = new AtomicInteger();  
System.out.println(atomicInteger.getAndAdd(10));  
System.out.println(atomicInteger.addAndGet(10));  
```

本示例将打印出 0 和 20。例子中，第二行拿到的是加 10 之前的 AtomicInteger 的值。加 10 之前的值是 0。第三行将 AtomicInteger 的值再加 10，并返回加操作之后的值。该值现在是为 20。

你当然也可以使用这俩方法为 AtomicInteger 添加负值。结果实际是一个减法操作。

getAndIncrement() 和 incrementAndGet() 方法类似于 getAndAdd() 和 addAndGet()，但每次只将 AtomicInteger 的值加 1。

## 减小 AtomicInteger 的值
AtomicInteger 类还提供了一些减小 AtomicInteger 的值的原子性方法。这些方法是：
- decrementAndGet()
- getAndDecrement()

decrementAndGet() 将 AtomicInteger 的值减一，并返回减一后的值。getAndDecrement() 也将 AtomicInteger 的值减一，但它返回的是减一之前的值。

# 原子性长整型 AtomicLong
AtomicLong 类为我们提供了一个可以进行原子性读和写操作的 long 变量，它还包含一系列先进的原子性操作，比如 compareAndSet()AtomicLong 类位于 java.util.concurrent.atomic 包，因此其完整类名为 java.util.concurrent.atomic.AtomicLong。

## 创建一个 AtomicLong 
创建一个 AtomicLong 如下：

```java
AtomicLong atomicLong = new AtomicLong();  
```

将创建一个初始值为 0 的 AtomicLong。 

如果你想创建一个指定初始值的 AtomicLong，可以：

```java
AtomicLong atomicLong = new AtomicLong(123);  
```

本示例将 123 作为参数传递给 AtomicLong 的构造子，后者将 AtomicLong 实例的初始值设置为 123。 

## 获取 AtomicLong 的值 
你可以通过 get() 方法获取 AtomicLong 的值。AtomicLong.get() 示例：

```java
AtomicLong atomicLong = new AtomicLong(123);  
 
long theValue = atomicLong.get();  
```

## 设置 AtomicLong 的值 
你可以通过 set() 方法设置 AtomicLong 实例的值。一个 AtomicLong.set() 的示例：

```java
AtomicLong atomicLong = new AtomicLong(123);  
 
atomicLong.set(234);  
```

本示例新建了一个初始值为 123 的 AtomicLong，第二行将其值设置为 234。

## 比较并设置 AtomicLong 的值
AtomicLong 类也有一个原子性的 compareAndSet() 方法。这一方法将 AtomicLong 实例的当前值与一个期望值进行比较，如果两种相等，为 AtomicLong 实例设置一个新值。AtomicLong.compareAndSet() 使用示例：

```java
AtomicLong atomicLong = new AtomicLong(123);  
 
long expectedValue = 123;  
long newValue      = 234;  
atomicLong.compareAndSet(expectedValue, newValue);  
```

本示例新建了一个初始值为 123 的 AtomicLong。然后将 AtomicLong 的当前值与期望值 123 进行比较，如果相等的话，AtomicLong 的新值将变为 234。

## 增加 AtomicLong 值
AtomicLong 具备一些能够增加 AtomicLong 的值并返回自身值的方法。这些方法如下：
- addAndGet()
- getAndAdd()
- getAndIncrement()
- incrementAndGet()

第一个方法 addAndGet() 将 AtomicLong 的值加一个数字，并返回增加后的值。第二个方法 getAndAdd() 也将 AtomicLong 的值加一个数字，但返回的是增加前的 AtomicLong 的值。具体使用哪一个取决于你自己的场景。示例如下：

```java
AtomicLong atomicLong = new AtomicLong();  
System.out.println(atomicLong.getAndAdd(10));  
System.out.println(atomicLong.addAndGet(10));  
```

本示例将打印出 0 和 20。例子中，第二行拿到的是加 10 之前的 AtomicLong 的值。加 10 之前的值是 0。第三行将 AtomicLong 的值再加 10，并返回加操作之后的值。该值现在是为 20。

你当然也可以使用这俩方法为 AtomicLong 添加负值。结果实际是一个减法操作。

getAndIncrement() 和 incrementAndGet() 方法类似于 getAndAdd() 和 addAndGet()，但每次只将 AtomicLong 的值加 1。

## 减小 AtomicLong 的值
AtomicLong 类还提供了一些减小 AtomicLong 的值的原子性方法。这些方法是：
- decrementAndGet()
- getAndDecrement()

decrementAndGet() 将 AtomicLong 的值减一，并返回减一后的值。getAndDecrement() 也将 AtomicLong 的值减一，但它返回的是减一之前的值。

# 原子性引用型 AtomicReference
AtomicReference 提供了一个可以被原子性读和写的对象引用变量。原子性的意思是多个想要改变同一个 AtomicReference 的线程不会导致 AtomicReference 处于不一致的状态。AtomicReference 还有一个 compareAndSet() 方法，通过它你可以将当前引用于一个期望值(引用)进行比较，如果相等，在该 AtomicReference 对象内部设置一个新的引用。

## 创建一个 AtomicReference
创建 AtomicReference 如下：

```java
AtomicReference atomicReference = new AtomicReference();  
```

如果你需要使用一个指定引用创建 AtomicReference，可以：

```java
String initialReference = "the initially referenced string";  
AtomicReference atomicReference = new AtomicReference(initialReference);  
```

## 创建泛型 AtomicReference
你可以使用 Java 泛型来创建一个泛型 AtomicReference。示例：

```java
AtomicReference<String> atomicStringReference =  
    new AtomicReference<String>();  
```

你也可以为泛型 AtomicReference 设置一个初始值。示例：

```java
String initialReference = "the initially referenced string";  
AtomicReference<String> atomicStringReference =  new AtomicReference<String>(initialReference);  
```

## 获取 AtomicReference 引用
你可以通过 AtomicReference 的 get() 方法来获取保存在 AtomicReference 里的引用。如果你的 AtomicReference 是非泛型的，get() 方法将返回一个 Object 类型的引用。如果是泛型化的，get() 将返回你创建 AtomicReference 时声明的那个类型。

先来看一个非泛型的 AtomicReference get() 示例：

```java
AtomicReference atomicReference = new AtomicReference("first value referenced");  
String reference = (String) atomicReference.get();  
```

注意如何对 get() 方法返回的引用强制转换为 String。 

泛型化的 AtomicReference 示例：

```java
AtomicReference<String> atomicReference =   new AtomicReference<String>("first value referenced");
String reference = atomicReference.get();  
```

编译器知道了引用的类型，所以我们无需再对 get() 返回的引用进行强制转换了。

## 设置 AtomicReference 引用
你可以使用 get() 方法对 AtomicReference 里边保存的引用进行设置。如果你定义的是一个非泛型 AtomicReference，set() 将会以一个 Object 引用作为参数。如果是泛型化的 AtomicReference，set() 方法将只接受你定义给的类型。

AtomicReference set() 示例：

```java
AtomicReference atomicReference = new AtomicReference();  
atomicReference.set("New object referenced");  
```

这个看起来非泛型和泛型化的没啥区别。真正的区别在于编译器将对你能够设置给一个泛型化的 AtomicReference 参数类型进行限制。

## 比较并设置 AtomicReference 引用
AtomicReference 类具备了一个很有用的方法：compareAndSet()。compareAndSet() 可以将保存在 AtomicReference 里的引用于一个期望引用进行比较，如果两个引用是一样的(并非 equals() 的相等，而是 == 的一样)，将会给AtomicReference 实例设置一个新的引用。

如果 compareAndSet() 为 AtomicReference 设置了一个新的引用，compareAndSet() 将返回 true。否则compareAndSet() 返回 false。

AtomicReference compareAndSet() 示例：

```java
String initialReference = "initial value referenced";  
 
AtomicReference<String> atomicStringReference =  
    new AtomicReference<String>(initialReference);  
 
String newReference = "new value referenced";  
boolean exchanged = atomicStringReference.compareAndSet(initialReference, newReference);  
System.out.println("exchanged: " + exchanged);  
 
exchanged = atomicStringReference.compareAndSet(initialReference, newReference);  
System.out.println("exchanged: " + exchanged);  
```

本示例创建了一个带有一个初始引用的泛型化的 AtomicReference。之后两次调用 comparesAndSet()来对存储值和期望值进行对比，如果二者一致，为 AtomicReference 设置一个新的引用。第一次比较，存储的引用(initialReference)和期望的引用(initialReference)一致，所以一个新的引用(newReference)被设置给 AtomicReference，compareAndSet() 方法返回 true。第二次比较时，存储的引用(newReference)和期望的引用(initialReference)不一致，因此新的引用没有被设置给 AtomicReference，compareAndSet() 方法返回 false。
