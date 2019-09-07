# 交换机 Exchanger
java.util.concurrent.Exchanger 类表示一种两个线程可以进行互相交换对象的会和点。

两个线程通过一个 Exchanger 交换对象。

交换对象的动作由 Exchanger 的两个 exchange() 方法的其中一个完成。以下是一个示例：

```java
Exchanger exchanger = new Exchanger();  
 
ExchangerRunnable exchangerRunnable1 =  
        new ExchangerRunnable(exchanger, "A");  
 
ExchangerRunnable exchangerRunnable2 =  
        new ExchangerRunnable(exchanger, "B");  
 
new Thread(exchangerRunnable1).start();  
new Thread(exchangerRunnable2).start();  
```

ExchangerRunnable 代码：

```java
public class ExchangerRunnable implements Runnable{  
 
    Exchanger exchanger = null;  
    Object object = null;  
 
    public ExchangerRunnable(Exchanger exchanger, Object object) {  
        this.exchanger = exchanger;  
        this.object = object;  
    }  
 
    public void run() {  
        try {  
            Object previous = this.object;  
 
            this.object = this.exchanger.exchange(this.object);  
 
            System.out.println(  
                    Thread.currentThread().getName() +  
                    " exchanged " + previous + " for " + this.object  
            );  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  
}  
```

以上程序输出：

```
Thread-0 exchanged A for B
Thread-1 exchanged B for A
```
