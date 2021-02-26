---
title: Java Concurrent
date: 2021-02-23 21:11:22 +0800
categories: [java, concurrent]
tags: [concurrent]
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
native methods :methods written in a language other than the Java programming language



