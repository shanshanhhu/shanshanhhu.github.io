---
title: Java Concurrency
date: 2021-02-23 21:11:22 +0800
categories: [java, concurrency]
tags: [concurrency]
---

# 进程 & 线程 [Source](https://blog.csdn.net/ThinkWon/article/details/102021274)
- **进程**
一个在内存中运行的应用程序。每个进程都有自己独立的一块内存空间，一个进程可以有多个线程，
- **线程**
进程中的一个执行任务（控制单元），负责当前进程中程序的执行。一个进程至少有一个线程，一个进程可以运行多个线程，多个线程可共享数据。
与进程不同的是同一进程的多个线程共享进程的**堆和方法区资源**，但每个线程有自己的**程序计数器、虚拟机栈和本地方法栈**，所以系统在产生一个线程，或是在各个线程之间作切换工作时，负担要比进程小得多，也正因为如此，线程也被称为轻量级进程。
- 进程 vs 线程
1. 根本区别：进程是操作系统资源分配的基本单位，而线程是cpu任务调度和执行的基本单位.
2. 资源开销：每个进程都有独立的代码和数据空间（程序上下文），程序之间的切换会有较大的开销；线程可以看做轻量级的进程，同一类线程共享代码和数据空间，每个线程都有自己独立的运行栈和程序计数器（PC），线程之间切换的开销小。
3. 内存分配：同一进程的线程共享本进程的地址空间和资源，而进程之间的地址空间和资源是相互独立的
4. 包含关系：如果一个进程内有多个线程，则执行过程不是一条线的，而是多条线（线程）共同完成的；线程是进程的一部分，所以线程也被称为轻权进程或者轻量级进程。
5. 影响关系：一个进程崩溃后，在保护模式下不会对其他进程产生影响，但是一个线程崩溃整个进程都死掉。所以多进程要比多线程健壮。
![process](../assets/img/sample/process.png)
-  程序计数器为什么是私有的?
The program counter (PC) holds the address of the next instruction to be executed.
程序计数器主要有下面两个作用：
字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。
所以，**程序计数器私有主要是为了线程切换后能恢复到正确的执行位置**。

- 虚拟机栈和本地方法栈为什么是私有的?
虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。 在 HotSpot 虚拟机中和 Java 虚拟机栈合二为一。
为了保证线程中的局部变量不被别的线程访问到，虚拟机栈和本地方法栈是线程私有的。
![java stacks](../assets/img/sample/java-stacks.gif)
**Frame** :[The Structure of the Java Virtual Machine](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html)
A frame is used to store data and partial results, as well as to perform dynamic linking, return values for methods, and dispatch exceptions.
A new frame is created each time a method is invoked. A frame is destroyed when its method invocation completes, whether that completion is normal or abrupt (it throws an uncaught exception). Frames are allocated from the Java Virtual Machine stack (§2.5.2) of the thread creating the frame. Each frame has its own array of local variables , its own operand stack , and a reference to the run-time constant pool of the class of the current method.
**Java Virtual Machine Stacks**：
A Java Virtual Machine stack stores frames. A Java Virtual Machine stack is analogous to the stack of a conventional language such as C: it holds local variables and partial results, and plays a part in method invocation and return. Because the Java Virtual Machine stack is never manipulated directly except to push and pop frames, frames may be heap allocated. The memory for a Java Virtual Machine stack does not need to be contiguous.
**Native Method Stacks**：
native methods :methods written in a language other than the Java programming language.

- 并发（Concurrency） & 并行（Parallelism）
  - 并发:一个处理器同时处理多个任务, 并发事件之间不一定要同一时刻发生。
  - 并行:多个处理器或者是多核的处理器同时处理多个不同的任务。是指同时发生的两个并发事件。
> 前者是逻辑上的同时发生（simultaneous），而后者是物理上的同时发生．
![](../assets/img/sample/concurrency.webp)

- 为什么要使用多线程呢?
从计算机底层来说： 线程可以比作是轻量级的进程，是程序执行的最小单位,线程间的切换和调度的成本远远小于进程。另外，多核 CPU 时代意味着多个线程可以同时运行，这减少了线程上下文切换的开销。
从当代互联网发展趋势来说： 现在的系统要求百万级甚至千万级的并发量，而多线程并发编程正是开发高并发系统的基础，利用好多线程机制可以大大提高系统整体的并发能力以及性能。

- 使用多线程可能带来什么问题?
并发编程的目的就是为了能提高程序的执行效率提高程序运行速度，但是并发编程并不总是能提高程序运行速度的，而且并发编程可能会遇到很多问题，比如：内存泄漏、死锁、线程不安全等等。

- 什么是上下文切换?
多线程编程中一般线程的个数都大于 CPU 核心的个数，而一个 CPU 核心在任意时刻只能被一个线程使用，为了让这些线程都能得到有效执行，CPU 采取的策略是为每个线程分配时间片并轮转的形式。当一个线程的时间片用完的时候就会重新处于就绪状态让给其他线程使用，这个过程就属于一次上下文切换。
当前任务在执行完 CPU 时间片切换到另一个任务之前会先保存自己的状态，以便下次再切换回这个任务时，可以再加载这个任务的状态。任务从保存到再加载的过程就是一次上下文切换。
## Java Thread Life Cycle and Thread States
[Details:](https://howtodoinjava.com/java/multi-threading/java-thread-life-cycle-and-thread-states/)
![](../assets/img/sample/Java-Thraed-Life-Cycle-States.jpg)
![](../assets/img/sample/RUNNABLE-VS-RUNNING.png)

## Volatile Keyword
[volatile:](http://tutorials.jenkov.com/java-concurrency/volatile.html)The Java volatile keyword is used to mark a Java variable as "being stored in main memory". More precisely that means, that every read of a volatile variable will be read from the computer's main memory, and not from the CPU cache, and that every write to a volatile variable will be written to main memory, and not just to the CPU cache.
- The Java volatile keyword guarantees **visibility** of changes to variables across threads.
- To address the instruction reordering challenge, the Java volatile keyword gives a **"happens-before"** guarantee
## Optimistic and Pessimistic Locking
  1. Pessimistic Locking --- `synchronized` keywords. It asks you to first guarantee that no other thread will interfere in between certain operation (i.e. lock the object), and then only allow you access to any instance/method. It put a significant penalty on your application in terms of performance. Reason is simple that waiting threads can not do anything unless they also get a chance and perform the guarded operation.
  2. Optimistic Locking --- `CAS` algorithm. There exist one more approach which is more efficient in performance, and it optimistic in nature. In this approach, you proceed with an update, being hopeful that you can complete it without interference. This approach relies on collision detection to determine if there has been interference from other parties during the update, in which case the operation fails and can be retried (or not).
  3. CAS (Compare and Swap) algorithm. It used by atomic constructs like `AtomicInteger`, `AtomicLong` etc. This algorithm compares the contents of a memory location to a given value and, only if they are the same, modifies the contents of that memory location to a given new value. This is done as a single atomic operation. The atomicity guarantees that the new value is calculated based on up-to-date information; if the value had been updated by another thread in the meantime, the write would fail.
  [Details:How CAS (Compare And Swap) in Java works](https://dzone.com/articles/how-cas-compare-and-swap-java)
```
public class MyApp
{
    private AtomicInteger count = new AtomicInteger(0);
    public void upateVisitors()
    {
       count.incrementAndGet(); //increment the visitors count
    }
}
public final long incrementAndGet() {
    for (;;) {
        long current = get();
        long next = current + 1;
        if (compareAndSet(current, next))
          return next;
    }
}
// In JDK 8 the above code has been changed to a single intrinsic:
public final long incrementAndGet() {
        return unsafe.getAndAddLong(this, valueOffset, 1L) + 1L;
}
```
4. `LongAdder` introduced in Java8. This class is usually preferable to AtomicLong when multiple threads update a common sum that is used for purposes such as collecting statistics, not for fine-grained synchronization control. Under low update contention, the two classes have similar characteristics. But under high contention, expected throughput of this class is significantly higher, at the expense of higher space consumption. So `LongAdder` is not always a replacement for `AtomicLong`.

## `synchronized` keywords
A synchronized block of code can only be executed by one thread at a time.
- Syntax [Java synchronized keyword](https://howtodoinjava.com/java/keywords/java-synchronized/)
```
// code block
synchronized( lockObject )
{
   // synchronized statements
}
// method
<access modifier> synchronized method( parameters )
{
    // synchronized code
}
```
When a thread wants to execute synchronized statements inside the synchronized block, it MUST acquire the lock on lockObject‘s monitor. At a time, only one thread can acquire the monitor of a lock object. So all other threads must wait till this thread, currently acquired the lock, finish it’s execution.
Keep in mind that if a thread is put on sleep (using sleep() method) then it does not release the lock. At this sleeping time, no thread will be executing the synchronized block statements.
Java synchronization will throw NullPointerException if lock object used in 'synchronized (lock)' is null.
```java
public class MathClass
{
    void printNumbers(int n) throws InterruptedException
    {
        synchronized (this)
        {
            for (int i = 1; i <= n; i++)
            {
                System.out.println(Thread.currentThread().getName() + " :: "+  i);
                Thread.sleep(500);
            }
        }
    }

    // method
    synchronized void printNumbers(int n) throws InterruptedException
    {
        for (int i = 1; i <= n; i++)
        {
            System.out.println(Thread.currentThread().getName() + " :: "+  i);
            Thread.sleep(500);
        }
    }

}

public class Main
{
    public static void main(String args[])
    {
        final MathClass mathClass = new MathClass();

        //first thread
        Runnable r = new Runnable()
        {
            public void run()
            {
                try {
                    mathClass.printNumbers(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        new Thread(r, "ONE").start();
        new Thread(r, "TWO").start();
    }
}
// console
//ONE :: 1
//ONE :: 2
//ONE :: 3
//
//TWO :: 1
//TWO :: 2
//TWO :: 3
```
### `synchronized` Object level lock vs Class level lock in Java
- **Object level lock** is mechanism when we want to synchronize a non-static method or non-static code block such that only one thread will be able to execute the code block on given instance of the class. This should always be done to make instance level data thread safe.
```
public class DemoClass
{
    public synchronized void demoMethod(){}
}

or

public class DemoClass
{
    public void demoMethod(){
        synchronized (this)
        {
            //other thread safe code
        }
    }
}

```
- **Class level locking** should always be done to make static data thread safe. As we know that static keyword associate data of methods to class level, so use locking at static fields or methods to make it on class level.
```
public class DemoClass
{
    //Method is static
    public synchronized static void demoMethod(){

    }
}

or

public class DemoClass
{
    public void demoMethod()
    {
        //Acquire lock on .class reference
        synchronized (DemoClass.class)
        {
            //other thread safe code
        }
    }
}
```
[Notes](https://howtodoinjava.com/java/multi-threading/object-vs-class-level-locking/)

## wait(), notify() and notifyAll()
[how to work with wait(), notify() and notifyAll()](https://howtodoinjava.com/java/multi-threading/wait-notify-and-notifyall-methods/)
- wait(): It tells the calling thread to give up the lock and go to sleep until some other thread enters the same monitor and calls notify()
- notify(): It wakes up one single thread that called wait() on the same object. It should be noted that calling notify() does not actually give up a lock on a resource. It tells a waiting thread that that thread can wake up. However, the lock is not actually given up until the notifier’s synchronized block has completed.
- notifyAll(): It wakes up all the threads that called wait() on the same object. The highest priority thread will run first in most of the situation, though not guaranteed. Other things are same as notify() method above.
Usage: solve a producer consumer problem.
```java
class Producer implements Runnable
{
   private final List<Integer> taskQueue;
   private final int           MAX_CAPACITY;

   public Producer(List<Integer> sharedQueue, int size)
   {
      this.taskQueue = sharedQueue;
      this.MAX_CAPACITY = size;
   }

   @Override
   public void run()
   {
      int counter = 0;
      while (true)
      {
         try
         {
            produce(counter++);
         }
         catch (InterruptedException ex)
         {
            ex.printStackTrace();
         }
      }
   }

   private void produce(int i) throws InterruptedException
   {
      synchronized (taskQueue)
      {
         while (taskQueue.size() == MAX_CAPACITY)
         {
            System.out.println("Queue is full " + Thread.currentThread().getName() + " is waiting , size: " + taskQueue.size());
            taskQueue.wait();
         }

         Thread.sleep(1000);
         taskQueue.add(i);
         System.out.println("Produced: " + i);
         taskQueue.notifyAll();
      }
   }
}
```
```java
class Consumer implements Runnable
{
   private final List<Integer> taskQueue;

   public Consumer(List<Integer> sharedQueue)
   {
      this.taskQueue = sharedQueue;
   }

   @Override
   public void run()
   {
      while (true)
      {
         try
         {
            consume();
         } catch (InterruptedException ex)
         {
            ex.printStackTrace();
         }
      }
   }

   private void consume() throws InterruptedException
   {
      synchronized (taskQueue)
      {
         while (taskQueue.isEmpty())
         {
            System.out.println("Queue is empty " + Thread.currentThread().getName() + " is waiting , size: " + taskQueue.size());
            taskQueue.wait();
         }
         Thread.sleep(1000);
         int i = (Integer) taskQueue.remove(0);
         System.out.println("Consumed: " + i);
         taskQueue.notifyAll();
      }
   }
}
```
```java
public class ProducerConsumerExampleWithWaitAndNotify
{
   public static void main(String[] args)
   {
      List<Integer> taskQueue = new ArrayList<Integer>();
      int MAX_CAPACITY = 5;
      Thread tProducer = new Thread(new Producer(taskQueue, MAX_CAPACITY), "Producer");
      Thread tConsumer = new Thread(new Consumer(taskQueue), "Consumer");
      tProducer.start();
      tConsumer.start();
   }
}
```

## sleep() vs wait()
[Source:](https://howtodoinjava.com/java/multi-threading/sleep-vs-wait/)
**sleep()** is a method which is used to pause the process for few seconds or the time we want to. But in case of **wait()** method, thread goes in waiting state and it won’t come back automatically until we call the notify() or notifyAll().
The major difference is that wait() releases the lock or monitor while sleep() doesn’t releases the lock or monitor while waiting. wait() is used for inter-thread communication while sleep() is used to introduce pause on execution, generally.
While sleep() is a static method which means that it always affects the current thread (the one that is executing the sleep method). A common mistake is to call t.sleep() where t is a different thread; even then, it is the current thread that will sleep, not the t thread.
1. Method called on
wait() – Call on an object; a current thread must synchronize on the lock object.
sleep() – Call on a Thread; always currently executing thread.
2. Synchronized
wait() – when synchronized multiple threads access same Object one by one.
sleep() – when synchronized multiple threads wait for sleep over of sleeping thread.
3. Lock duration
wait() – release the lock for other objects to have chance to execute.
sleep() – keep lock for at least t times if timeout specified or somebody interrupt.
4. wake up condition
wait() – until call notify(), notifyAll() from object
sleep() – until at least time expire or call interrupt().
5. Usage
sleep() – for time-synchronization
wait() – for multi-thread-synchronization.

## yield() and join()
- A yielding thread tells the virtual machine that it’s willing to let other threads be scheduled in its place. This indicates that it’s not doing something too critical. Note that it’s only a hint, though, and not guaranteed to have any effect at all.
- The join() method of a Thread instance can be used to “join” the start of a thread’s execution to the end of another thread’s execution so that a thread will not start running until another thread has ended. If join() is called on a Thread instance, the currently running thread will block until the Thread instance has finished executing.Giving a timeout within join(), will make the join() effect to be nullified after the specific timeout.

## Java Callable Future Example
[Details:](https://howtodoinjava.com/java/multi-threading/java-callable-future-example/)

## Java Thread Pool
[Java Pooling Practice in meituan](https://tech.meituan.com/2020/04/02/java-pooling-pratice-in-meituan.html)
[Design and thinking of Microservices flow limit](https://mp.weixin.qq.com/s?__biz=MzI4MTY5NTk4Ng==&mid=2247488993&idx=1&sn=4b9d5deedd0e626c456744f04b499bbb&source=41#wechat_redirect)
![ThreadPoolExecutor](../assets/img/sample/ThreadPoolExecutor.png)
- Throttling
Throttling is the capability of regulating the rate of input for a system where output rate is slower than input. It is necessary to stop the system from crashing or resource exhaustion.
[Java ThreadPoolExecutor and BlockingQueue Example](https://howtodoinjava.com/java/multi-threading/how-to-use-blockingqueue-and-threadpoolexecutor-in-java/)
```java
public class DemoTask implements Runnable
{
    private String name = null;

    public DemoTask(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    @Override
    public void run() {
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Executing : " + name);
    }
}

```
```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

public class CustomThreadPoolExecutor extends ThreadPoolExecutor {

    public CustomThreadPoolExecutor(int corePoolSize, int maximumPoolSize,
            long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) {
        super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue);
    }

    @Override
    protected void beforeExecute(Thread t, Runnable r) {
        super.beforeExecute(t, r);
        System.out.println("Perform beforeExecute() logic");
    }

    @Override
    protected void afterExecute(Runnable r, Throwable t) {
        super.afterExecute(r, t);
        if (t != null) {
            System.out.println("Perform exception handler logic");
        }
        System.out.println("Perform afterExecute() logic");
    }

}
```
```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.RejectedExecutionHandler;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

public class DemoExecutor
{
    public static void main(String[] args)
    {
        Integer threadCounter = 0;
        BlockingQueue<Runnable> blockingQueue = new ArrayBlockingQueue<Runnable>(50);

        CustomThreadPoolExecutor executor = new CustomThreadPoolExecutor(10,
                                            20, 5000, TimeUnit.MILLISECONDS, blockingQueue);

        executor.setRejectedExecutionHandler(new RejectedExecutionHandler() {
            @Override
            public void rejectedExecution(Runnable r,
                    ThreadPoolExecutor executor) {
                System.out.println("DemoTask Rejected : "
                        + ((DemoTask) r).getName());
                System.out.println("Waiting for a second !!");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("Lets add another time : "
                        + ((DemoTask) r).getName());
                executor.execute(r);
            }
        });
        // Lets start all core threads initially
        executor.prestartAllCoreThreads();
        while (true) {
            threadCounter++;
            // Adding threads one by one
            System.out.println("Adding DemoTask : " + threadCounter);
            executor.execute(new DemoTask(threadCounter.toString()));

            if (threadCounter == 100)
                break;
        }
    }

}
```

## Runnable, Thread, Callable, Future.
In most cases, the `Runnable` interface should be used if you are planning to override the `run()` method and no other Thread methods.
The `Callable` interface is designed to define a task that returns a result and may throw an exception. While the `run` method of `Runnable` does not return a value.

- 创建线程有哪几种方式？
1. 继承**Thread类**创建线程类
定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。因此把run()方法称为执行体。
创建Thread子类的实例，即创建了线程对象。
调用线程对象的start()方法来启动该线程。

2. 通过**Runnable接口**创建线程类
定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
创建 Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象。
调用线程对象的start()方法来启动该线程。
```java
public class RunnableThreadTest implements Runnable
{

	private int i;
	public void run()
	{
		for(i = 0;i <100;i++)
		{
			 System.out.println(Thread.currentThread().getName()+" "+i);
		}
	}
	public static void main(String[] args)
	{
		for(int i = 0;i < 100;i++)
		{
        System.out.println(Thread.currentThread().getName()+" "+i);
        if(i==20)
        {
            RunnableThreadTest rtt = new RunnableThreadTest();
            new Thread(rtt,"新线程1").start();
            new Thread(rtt,"新线程2").start();
        }
		}
	}
}
```

3. 通过Callable和Future创建线程
创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程执行体，并且有返回值。
创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值。
使用FutureTask对象作为Thread对象的target创建并启动新线程。
调用FutureTask对象的get()方法来获得子线程执行结束后的返回值。
```java
package com.thread;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class CallableThreadTest implements Callable<Integer>
{

	public static void main(String[] args)
	{
		CallableThreadTest ctt = new CallableThreadTest();
		FutureTask<Integer> ft = new FutureTask<>(ctt);
		for(int i = 0;i < 100;i++)
		{
			System.out.println(Thread.currentThread().getName()+" 的循环变量i的值"+i);
			if(i==20)
			{
				new Thread(ft,"有返回值的线程").start();
			}
		}
		try
		{
			System.out.println("子线程的返回值："+ft.get());
		} catch (InterruptedException e)
		{
			e.printStackTrace();
		} catch (ExecutionException e)
		{
			e.printStackTrace();
		}

	}

	@Override
	public Integer call() throws Exception
	{
		int i = 0;
		for(;i<100;i++)
		{
			System.out.println(Thread.currentThread().getName()+" "+i);
		}
		return i;
	}

}
```

- 说一下 runnable 和 callable 有什么区别？
Runnable接口中的run()方法的返回值是void，它做的事情只是纯粹地去执行run()方法中的代码而已；
Callable接口中的call()方法是有返回值的，是一个泛型，和Future、FutureTask配合可以用来获取异步执行的结果。

## Best Practices of multi-threaded application
[Java executor framework tutorial and best practices](https://howtodoinjava.com/java/multi-threading/executor-framework-tutorial/)

## Java Inter-thread Communication --- PipedReader and PipedWriter
[Source:](https://howtodoinjava.com/java/multi-threading/inter-thread-communication-using-piped-streams-in-java/)
```java
public class PipeReaderThread implements Runnable
{
    PipedReader pr;
    String name = null;

    public PipeReaderThread(String name, PipedReader pr)
    {
        this.name = name;
        this.pr = pr;
    }

    public void run()
    {
        try {
            // continuously read data from stream and print it in console
            while (true) {
                char c = (char) pr.read(); // read a char
                if (c != -1) { // check for -1 indicating end of file
                    System.out.print(c);
                }
            }
        } catch (Exception e) {
            System.out.println(" PipeThread Exception: " + e);
        }
    }
}
```
```java
public class PipeWriterThread implements Runnable
{
    PipedWriter pw;
    String name = null;

    public PipeWriterThread(String name, PipedWriter pw) {
        this.name = name;
        this.pw = pw;
    }

    public void run() {
        try {
            while (true) {
                // Write some data after every two seconds
                pw.write("Testing data written...n");
                pw.flush();
                Thread.sleep(2000);
            }
        } catch (Exception e) {
            System.out.println(" PipeThread Exception: " + e);
        }
    }
}
```
```java
import java.io.*;

public class PipedCommunicationTest {
    public static void main(String[] args)
    {
        new PipedCommunicationTest();
    }

    public PipedCommunicationTest()
    {
        try
        {
            // Create writer and reader instances
            PipedReader pr = new PipedReader();
            PipedWriter pw = new PipedWriter();

            // Connect the writer with reader
            pw.connect(pr);

            // Create one writer thread and one reader thread
            Thread thread1 = new Thread(new PipeReaderThread("ReaderThread", pr));

            Thread thread2 = new Thread(new PipeWriterThread("WriterThread", pw));

            // start both threads
            thread1.start();
            thread2.start();

        }
        catch (Exception e)
        {
            System.out.println("PipeThread Exception: " + e);
        }
    }
}
```

# 支持并发的数据结构
- CopyOnWriteArraySet
[Java多线程系列--CopyOnWriteArraySet](https://www.cnblogs.com/xiaolovewei/p/9142046.html)
CopyOnWriteArraySet具有以下特性：
1. 它最适合于具有以下特征的应用程序：Set 大小通常保持很小，只读操作远多于可变操作，需要在遍历期间防止线程间的冲突。
2. 它是线程安全的。
3. 因为通常需要复制整个基础数组，所以可变操作（add()、set() 和 remove() 等等）的开销很大。
4. 迭代器支持hasNext(), next()等不可变操作，但不支持可变 remove()等 操作。
5. 使用迭代器进行遍历的速度很快，并且不会与其他线程发生冲突。在构造迭代器时，迭代器依赖于不变的数组快照。

## DeadLock
**死锁** :是指两个或两个以上的进程在执行过程中,因争夺资源而造成的一种互相等待的现象,若无外力作用,它们都将无法推进下去.
- 产生死锁的原因：
1. 因为系统资源不足。
2. 进程运行推进顺序不合适。
3. 资源分配不当等。
- 死锁产生的四个必要条件：
1. **互斥**条件：一个资源每次只能被一个进程使用。
2. **请求与保持**条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
3. **不剥夺**条件:进程已获得的资源，在末使用完之前，不能强行剥夺。
4. **循环等待**条件:若干进程之间形成一种头尾相接的循环等待资源关系。
- 如何避免死锁？
1. 加锁顺序（线程按照一定的顺序加锁）
2. 加锁时限（线程尝试获取锁的时候加上一定的时限，超过时限则放弃对该锁的请求，并释放自己占有的锁）
另外一个可以避免死锁的方法是在尝试获取锁的时候加一个超时时间，这也就意味着在尝试获取锁的过程中若超过了这个时限该线程则放弃对该锁请求。若一个线程没有在给定的时限内成功获得所有需要的锁，则会进行回退并释放所有已经获得的锁，然后等待一段随机的时间再重试。这段随机的等待时间让其它线程有机会尝试获取相同的这些锁，并且让该应用在没有获得锁的时候可以继续运行
3. 死锁检测


- 线程有哪些状态？
线程通常都有五种状态，创建、就绪、运行、阻塞和死亡。
创建状态。在生成线程对象，并没有调用该对象的start方法，这是线程处于创建状态。
就绪状态。当调用了线程对象的start方法之后，该线程就进入了就绪状态，但是此时线程调度程序还没有把该线程设置为当前线程，此时处于就绪状态。在线程运行之后，从等待或者睡眠中回来之后，也会处于就绪状态。
运行状态。线程调度程序将处于就绪状态的线程设置为当前线程，此时线程就进入了运行状态，开始运行run函数当中的代码。
阻塞状态。线程正在运行的时候，被暂停，通常是为了等待某个时间的发生(比如说某项资源就绪)之后再继续运行。sleep,suspend，wait等方法都可以导致线程阻塞。
死亡状态。如果一个线程的run方法执行结束或者调用stop方法后，该线程就会死亡。对于已经死亡的线程，无法再使用start方法令其进入就绪 　　


- sleep() 和 wait() 有什么区别？
sleep()：方法是线程类（Thread）的静态方法，让调用线程进入睡眠状态，让出执行机会给其他线程，等到休眠时间结束后，线程进入就绪状态和其他线程一起竞争cpu的执行时间。因为sleep() 是static静态的方法，他不能改变对象的机锁，当一个synchronized块中调用了sleep() 方法，线程虽然进入休眠，但是对象的机锁没有被释放，其他线程依然无法访问这个对象。
wait()：wait()是Object类的方法，当一个线程执行到wait方法时，它就进入到一个和该对象相关的等待池，同时释放对象的机锁，使得其他线程能够访问，可以通过notify，notifyAll方法来唤醒等待的线程

- notify()和 notifyAll()有什么区别？
如果线程调用了对象的 wait()方法，那么线程便会处于该对象的等待池中，等待池中的线程不会去竞争该对象的锁。
当有线程调用了对象的 notifyAll()方法（唤醒所有 wait 线程）或 notify()方法（只随机唤醒一个 wait 线程），被唤醒的的线程便会进入该对象的锁池中，锁池中的线程会去竞争该对象锁。也就是说，调用了notify后只要一个线程会由等待池进入锁池，而notifyAll会将该对象等待池内的所有线程移动到锁池中，等待锁竞争。
优先级高的线程竞争到对象锁的概率大，假若某线程没有竞争到该对象锁，它还会留在锁池中，唯有线程再次调用 wait()方法，它才会重新回到等待池中。而竞争到对象锁的线程则继续往下执行，直到执行完了 synchronized 代码块，它会释放掉该对象锁，这时锁池中的线程会继续竞争该对象锁。

- 线程的 run()和 start()有什么区别？
每个线程都是通过某个特定Thread对象所对应的方法run()来完成其操作的，方法run()称为线程体。通过调用Thread类的start()方法来启动一个线程。
start()方法来启动一个线程，真正实现了多线程运行。这时无需等待run方法体代码执行完毕，可以直接继续执行下面的代码； 这时此线程是处于就绪状态， 并没有运行。 然后通过此Thread类调用方法run()来完成其运行状态， 这里方法run()称为线程体，它包含了要执行的这个线程的内容， Run方法运行结束， 此线程终止。然后CPU再调度其它线程。
run()方法是在本线程里的，只是线程里的一个函数,而不是多线程的。 如果直接调用run(),其实就相当于是调用了一个普通函数而已，直接待用run()方法必须等待run()方法执行完毕才能执行下面的代码，所以执行路径还是只有一条，根本就没有线程的特征，所以在多线程执行时要使用start()方法而不是run()方法。


44. 创建线程池有哪几种方式？

①. newFixedThreadPool(int nThreads)
创建一个固定长度的线程池，每当提交一个任务就创建一个线程，直到达到线程池的最大数量，这时线程规模将不再变化，当线程发生未预期的错误而结束时，线程池会补充一个新的线程。

②. newCachedThreadPool()
创建一个可缓存的线程池，如果线程池的规模超过了处理需求，将自动回收空闲线程，而当需求增加时，则可以自动添加新线程，线程池的规模不存在任何限制。

③. newSingleThreadExecutor()
这是一个单线程的Executor，它创建单个工作线程来执行任务，如果这个线程异常结束，会创建一个新的来替代它；它的特点是能确保依照任务在队列中的顺序来串行执行。

④. newScheduledThreadPool(int corePoolSize)
创建了一个固定长度的线程池，而且以延迟或定时的方式来执行任务，类似于Timer.

- 线程池都有哪些状态？
线程池有5种状态：Running、ShutDown、Stop、Tidying、Terminated。
![](../assets/img/sample/thread-pool-life-cycle.png)

- 线程池中 submit()和 execute()方法有什么区别？

接收的参数不一样
submit有返回值，而execute没有
submit方便Exception处理

47. 在 java 程序中怎么保证多线程的运行安全？
[从Java多线程可见性谈Happens-Before原则](https://segmentfault.com/a/1190000011458941)
线程安全在三个方面体现：
原子性：提供互斥访问，同一时刻只能有一个线程对数据进行操作，（atomic,synchronized）；
可见性：一个线程对主内存的修改可以及时地被其他线程看到，（synchronized,volatile）；
有序性：一个线程观察其他线程中的指令执行顺序，由于指令重排序，该观察结果一般杂乱无序，（happens-before原则）。

48. 多线程锁的升级原理是什么？

在Java中，锁共有4种状态，级别从低到高依次为：无状态锁，偏向锁，轻量级锁和重量级锁状态，这几个状态会随着竞争情况逐渐升级。锁可以升级但不能降级。

49. 什么是死锁？
死锁是指两个或两个以上的进程在执行过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程。是操作系统层面的一个错误，是进程死锁的简称，最早在 1965 年由 Dijkstra 在研究银行家算法时提出的，它是计算机操作系统乃至整个并发程序设计领域最难处理的问题之一。

50. 怎么防止死锁？

死锁的四个必要条件：
互斥条件：进程对所分配到的资源不允许其他进程进行访问，若其他进程访问该资源，只能等待，直至占有该资源的进程使用完成后释放该资源
请求和保持条件：进程获得一定的资源之后，又对其他资源发出请求，但是该资源可能被其他进程占有，此事请求阻塞，但又对自己获得的资源保持不放
不可剥夺条件：是指进程已获得的资源，在未完成使用之前，不可被剥夺，只能在使用完后自己释放
循环等待条件：是指进程发生死锁后，若干进程之间形成一种头尾相接的循环等待资源关系

这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之 一不满足，就不会发生死锁。

理解了死锁的原因，尤其是产生死锁的四个必要条件，就可以最大可能地避免、预防和 解除死锁。
所以，在系统设计、进程调度等方面注意如何不让这四个必要条件成立，如何确 定资源的合理分配算法，避免进程永久占据系统资源。
此外，也要防止进程在处于等待状态的情况下占用资源。因此，对资源的分配要给予合理的规划。
51. ThreadLocal 是什么？有哪些使用场景？
线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java提供ThreadLocal类来支持线程局部变量，是一种实现线程安全的方式。但是在管理环境下（如 web 服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。

52. 说一下 synchronized 底层实现原理？

synchronized可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变量的内存可见性。
Java中每一个对象都可以作为锁，这是synchronized实现同步的基础：

普通同步方法，锁是当前实例对象
静态同步方法，锁是当前类的class对象
同步方法块，锁是括号里面的对象

- 不需要synchronized的操作
JVM规范定义了几种原子操作：
基本类型（long和double除外）赋值，例如：int n = m；
引用类型赋值，例如：List<String> list = anotherList。
long和double是64位数据，JVM没有明确规定64位赋值操作是不是一个原子操作，不过在x64平台的JVM是把long和double的赋值作为原子操作实现的。

53. synchronized 和 volatile 的区别是什么？

volatile本质是在告诉jvm当前变量在寄存器（工作内存）中的值是不确定的，需要从主存中读取； synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。
volatile仅能使用在变量级别；synchronized则可以使用在变量、方法、和类级别的。
volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized则可以保证变量的修改可见性和原子性。
volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞。
volatile标记的变量不会被编译器优化；synchronized标记的变量可以被编译器优化。

54. synchronized 和 Lock 有什么区别？

首先synchronized是java内置关键字，在jvm层面，Lock是个java类；
synchronized无法判断是否获取锁的状态，Lock可以判断是否获取到锁；
synchronized会自动释放锁(a 线程执行完同步代码会释放锁 ；b 线程执行过程中发生异常会释放锁)，Lock需在finally中手工释放锁（unlock()方法释放锁），否则容易造成线程死锁；
用synchronized关键字的两个线程1和线程2，如果当前线程1获得锁，线程2线程等待。如果线程1阻塞，线程2则会一直等待下去，而Lock锁就不一定会等待下去，如果尝试获取不到锁，线程可以不用一直等待就结束了；
synchronized的锁可重入、不可中断、非公平，而Lock锁可重入、可判断、可公平（两者皆可）；
Lock锁适合大量同步的代码的同步问题，synchronized锁适合代码少量的同步问题。

55. synchronized 和 ReentrantLock 区别是什么？
synchronized是和if、else、for、while一样的关键字，ReentrantLock是类，这是二者的本质区别。既然ReentrantLock是类，那么它就提供了比synchronized更多更灵活的特性，可以被继承、可以有方法、可以有各种各样的类变量，ReentrantLock比synchronized的扩展性体现在几点上：
ReentrantLock可以对获取锁的等待时间进行设置，这样就避免了死锁
ReentrantLock可以获取各种锁的信息
ReentrantLock可以灵活地实现多路通知

可重入锁：对同一个线程，在获取到锁以后能够继续获取同一个锁。JVM允许同一个线程重复获取同一个锁，这种能被同一个线程反复获取的锁，就叫做可重入锁。
由于Java的线程锁是可重入锁，所以，获取锁的时候，不但要判断是否是第一次获取，还要记录这是第几次获取。每获取一次锁，记录+1，每退出synchronized块，记录-1，减到0的时候，才会真正释放锁。

另外，二者的锁机制其实也是不一样的:ReentrantLock底层调用的是Unsafe的park方法加锁，synchronized操作的应该是对象头中mark word。

56. 说一下 atomic 的原理？
Atomic包中的类基本的特性就是在多线程环境下，当有多个线程同时对单个（包括基本类型及引用类型）变量进行操作时，具有排他性，即当多个线程同时对该变量的值进行更新时，仅有一个线程能成功，而未成功的线程可以向自旋锁一样，继续尝试，一直等到执行成功。
Atomic系列的类中的核心方法都会调用unsafe类中的几个本地方法。我们需要先知道一个东西就是Unsafe类，全名为：sun.misc.Unsafe，这个类包含了大量的对C代码的操作，包括很多直接内存分配以及原子操作的调用，而它之所以标记为非安全的，是告诉你这个里面大量的方法调用都会存在安全隐患，需要小心使用，否则会导致严重的后果，例如在通过unsafe分配内存的时候，如果自己指定某些区域可能会导致一些类似C++一样的指针越界到其他进程的问题。
