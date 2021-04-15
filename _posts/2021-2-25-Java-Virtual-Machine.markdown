---
title: Java Virtual Machine
date: 2021-02-25 15:30:22 +0800
categories: [java, jvm]
tags: [jvm]
---

# The Structure of the Java Virtual Machine
[Official Documents](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html)

### The class File Format
Compiled code to be executed by the Java Virtual Machine is represented using a hardware- and operating system-independent binary format, typically (but not necessarily) stored in a file, known as the class file format. The class file format precisely defines the representation of a class or interface, including details such as byte ordering that might be taken for granted in a platform-specific object file format.
### Data Types
the Java Virtual Machine operates on two kinds of types: primitive types and reference types.
- Values of primitive types need not be tagged or otherwise be inspectable to determine their types at run time, or to be distinguished from values of reference types.
- the instruction set of the Java Virtual Machine distinguishes its operand types using instructions intended to operate on values of specific types.
- An object is either a dynamically allocated class instance or an array. A reference to an object is considered to have Java Virtual Machine type reference. Values of type reference can be thought of as pointers to objects. More than one reference to an object may exist. Objects are always operated on, passed, and tested via values of type reference.
### Primitive Types and Values
The primitive data types supported by the Java Virtual Machine are the *numeric types*, the `boolean` type , and the `returnAddress` type
The numeric types consist of the *integral types* and the *floating-point types*
1. **Integral Types and Values**
byte, short, int ,long, char
2. **Floating-Point Types, Value Sets, and Values**
- floating-point types : `float`, the 32-bit single-precision and `double`, 64-bit double-precision.
- two standard sets of floating-point values:  *float value set* and the *double value set*.  `float-extended-exponent value set` and the `double-extended-exponent value set`. These extended-exponent value sets may, under certain circumstances, be used instead of the standard value sets to represent the values of type float or double.
- Standard: IEEE Standard for Binary Floating-Point Arithmetic(ANSI/IEEE Std. 754-1985, New York).
The IEEE 754 standard includes not only positive and negative sign-magnitude numbers, but also positive and negative zeros, positive and negative infinities, and a special Not-a-Number value (hereafter abbreviated as "NaN"). The NaN value is used to represent the result of certain invalid operations such as dividing zero by zero.
- The float, float-extended-exponent, double, and double-extended-exponent value sets are not types.
3. **The returnAddress Type and Values**
The values of the returnAddress type are pointers to the opcodes of Java Virtual Machine instructions. Unlike the numeric primitive types, the returnAddress type cannot be modified by the running program.
4. **The boolean Type**
expressions in the Java programming language that operate on boolean values are compiled to use values of the Java Virtual Machine int data type.
boolean arrays in the Java programming language are encoded as Java Virtual Machine byte arrays, using 8 bits per boolean element. The Java Virtual Machine encodes boolean array components using 1 to represent true and 0 to represent false.
### Reference Types and Values
- three kinds of reference types: class types, array types, and interface types.
- Their values are references to dynamically created class instances, arrays, or class instances or arrays that implement interfaces, respectively.
- A special reference value : null reference, a reference to no object. The Java Virtual Machine specification does not mandate a concrete value encoding null.
### Run-Time Data Areas
The Java Virtual Machine defines various run-time data areas that are used during execution of a program. Some of these data areas are created on Java Virtual Machine start-up and are destroyed only when the Java Virtual Machine exits. Other data areas are per thread. Per-thread data areas are created when a thread is created and destroyed when the thread exits.
1. **The pc Register**
Each Java Virtual Machine thread has its own pc (program counter) register. If current executed method is not `native`, the pc register contains the address of the Java Virtual Machine instruction currently being executed. If the method currently being executed by the thread is `native`, the value of the Java Virtual Machine's pc register is undefined.
由于Java虚拟机的多线程是通过线程轮流切换、分配处理器执行时间的方式来实现的，在任何一个确定的时刻，一个处理器（对于多核处理器来说是一个内核）都只会执行一条线程中的指令。因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各条线程之间计数器互不影响，独立存储。
2. **Java Virtual Machine Stacks**
Each Java Virtual Machine thread has a private Java Virtual Machine stack, created at the same time as the thread. A Java Virtual Machine stack stores frames,  it holds local variables and partial results, and plays a part in method invocation and return.   The memory for a Java Virtual Machine stack does not need to be contiguous.
the Java Virtual Machine stack was known as the **Java stack**.
> A Java Virtual Machine implementation may provide the programmer or the user control over the initial size of Java Virtual Machine stacks, as well as, in the case of dynamically expanding or contracting Java Virtual Machine stacks, control over the maximum and minimum sizes.
- **exceptional conditions**:
  - If the computation in a thread requires a larger Java Virtual Machine stack than is permitted, the Java Virtual Machine throws a *StackOverflowError*.
  - If Java Virtual Machine stacks can be dynamically expanded, and expansion is attempted but insufficient memory can be made available to effect the expansion, or if insufficient memory can be made available to create the initial Java Virtual Machine stack for a new thread, the Java Virtual Machine throws an *OutOfMemoryError*.
3. **Heap**
- The JVM has a heap that is shared among all Java Virtual Machine threads. The heap is the run-time data area from which memory for all class instances and arrays is allocated.
- The heap is created on virtual machine start-up. Heap storage for objects is reclaimed by an automatic storage management system (known as a garbage collector); objects are never explicitly deallocated.
- The heap may be of a fixed size or may be expanded as required by the computation and may be contracted if a larger heap becomes unnecessary. The memory for the heap does not need to be contiguous.
> A Java Virtual Machine implementation may provide the programmer or the user control over the initial size of the heap, as well as, if the heap can be dynamically expanded or contracted, control over the maximum and minimum heap size.
- exceptional conditions:
  - If a computation requires more heap than can be made available by the automatic storage management system, the Java Virtual Machine throws an OutOfMemoryError.
4. **Method Area**
- The Java Virtual Machine has a method area that is shared among all Java Virtual Machine threads.
- It stores per-class structures such as the run-time constant pool, field and method data, and the code for methods and constructors, including the special methods used in class and instance initialization and interface initialization.
- The method area may be of a fixed size or may be expanded as required by the computation and may be contracted if a larger method area becomes unnecessary. The memory for the method area does not need to be contiguous.
- exceptional conditions:
  - If memory in the method area cannot be made available to satisfy an allocation request, the Java Virtual Machine throws an OutOfMemoryError.
5. Run-Time Constant Pool
- A run-time constant pool is a per-class or per-interface run-time representation of the constant_pool table in a class file
-  It contains several kinds of constants, ranging from numeric literals known at compile-time to method and field references that must be resolved at run-time.
- exceptional conditions:
  - When creating a class or interface, if the construction of the run-time constant pool requires more memory than can be made available in the method area of the Java Virtual Machine, the Java Virtual Machine throws an OutOfMemoryError.
6. Native Method Stacks
- An implementation of the Java Virtual Machine may use conventional stacks, colloquially called "C stacks," to support native methods (methods written in a language other than the Java programming language). Java Virtual Machine implementations that cannot load native methods and that do not themselves rely on conventional stacks need not supply native method stacks.
- native method stacks are typically allocated per thread when each thread is created.
- exceptional conditions:
  - StackOverflowError
  - OutOfMemoryError

### Frames
- A frame is used to store data and partial results, as well as to perform dynamic linking, return values for methods, and dispatch exceptions.
- A new frame is created each time a method is invoked. A frame is destroyed when its method invocation completes, whether that completion is normal or abrupt (it throws an uncaught exception). Frames are allocated from the Java Virtual Machine stack of the thread creating the frame. Each frame has its own array of local variables , its own operand stack , and a reference to the run-time constant pool of the class of the current method.
- The sizes of the local variable array and the operand stack are determined at compile-time and are supplied along with the code for the method associated with the frame . Thus the size of the frame data structure depends only on the implementation of the Java Virtual Machine, and the memory for these structures can be allocated simultaneously on method invocation.
- Only one frame, the frame for the executing method, is active at any point in a given thread of control.  This frame is referred to as the *current frame*, and its method is known as the *current method*. The class in which the current method is defined is the *current class*. Operations on local variables and the operand stack are typically with reference to the current frame.
- A frame ceases to be current if its method invokes another method or if its method completes.
- Note that a frame created by a thread is local to that thread and cannot be referenced by any other thread.
1. Local Variables
- Each frame contains an array of variables known as its local variables. The length of the local variable array of a frame is determined at compile-time and supplied in the binary representation of a class or interface along with the code for the method associated with the frame.
- A single local variable can hold a value of type boolean, byte, char, short, int, float, reference, or returnAddress. A pair of local variables can hold a value of type long or double.
- Local variables are addressed by indexing. The index of the first local variable is zero.
- A value of type long or type double occupies two consecutive local variables. Such a value may only be addressed using the lesser index. For example, a value of type double stored in the local variable array at index n actually occupies the local variables with indices n and n+1; however, the local variable at index n+1 cannot be loaded from. It can be stored into. However, doing so invalidates the contents of local variable n.
- The Java Virtual Machine uses local variables to pass parameters on method invocation. On class method invocation, any parameters are passed in consecutive local variables starting from local variable 0. On instance method invocation, local variable 0 is always used to pass a reference to the object on which the instance method is being invoked (this in the Java programming language). Any parameters are subsequently passed in consecutive local variables starting from local variable 1.
2. Operand Stacks
- Each frame contains a last-in-first-out (LIFO) stack known as its operand stack. The maximum depth of the operand stack of a frame is determined at compile-time and is supplied along with the code for the method associated with the frame.
- The operand stack is empty when the frame that contains it is created. The Java Virtual Machine supplies instructions to load constants or values from local variables or fields onto the operand stack. Other Java Virtual Machine instructions take operands from the operand stack, operate on them, and push the result back onto the operand stack. The operand stack is also used to prepare parameters to be passed to methods and to receive method results.
- Values from the operand stack must be operated upon in ways appropriate to their types.
- At any point in time, an operand stack has an associated depth, where a value of type long or double contributes two units to the depth and a value of any other type contributes one unit.
3. Dynamic Linking
Each frame (§2.6) contains a reference to the run-time constant pool (§2.5.5) for the type of the current method to support dynamic linking of the method code. The class file code for a method refers to methods to be invoked and variables to be accessed via symbolic references. Dynamic linking translates these symbolic method references into concrete method references, loading classes as necessary to resolve as-yet-undefined symbols, and translates variable accesses into appropriate offsets in storage structures associated with the run-time location of these variables.

This late binding of the methods and variables makes changes in other classes that a method uses less likely to break this code.

### HotSpot JVM
- [HotSpot Group](https://openjdk.java.net/groups/hotspot/) from which you can download HotSpot source code.

# Java Garbage Collection Basics
[Offical Tutorial：](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)
## Hotspot Architecture
![](/assets/img/sample/Hotspot-Architecture.PNG)
The main components of the JVM include the classloader, the runtime data areas, and the execution engine.
- Performance
Typically, when tuning a Java application, the focus is on one of two main goals: `responsiveness` or `throughput`. When tuning performance, Most tuning options relate to sizing the heap and choosing the most appropriate garbage collector for your situation. The JIT compiler also has a big impact on performance but rarely requires tuning with the newer versions of the JVM.
**Responsiveness**: refers to how quickly an application or system responds with a requested piece of data.
**Throughput**: focuses on maximizing the amount of work by an application in a specific period of time.

## Automatic Garbage Collection
Automatic garbage collection is the process of looking at heap memory, identifying which objects are in use and which are not, and deleting the unused objects.
### Basic GC Process
1. Marking
the garbage collector identifies which pieces of memory are in use and which are not.
![](/assets/img/sample/marking.PNG)
2. Deletion
- Normal Deletion
![](/assets/img/sample/Normal-Deletion.PNG)
- Deletion with Compacting
![](/assets/img/sample/Deletion-with-Compacting.PNG)

### Why Generational Garbage Collection?
As stated earlier, having to mark and compact all the objects in a JVM is inefficient. As more and more objects are allocated, the list of objects grows and grows leading to longer and longer garbage collection time. However, empirical analysis of applications has shown that most objects are short lived.

### JVM Generations
![](/assets/img/sample/JVM-Generations.PNG)
- The Young Generation is where all new objects are allocated and aged.
- The Old Generation is used to store long surviving objects.
- The Permanent generation contains metadata required by the JVM to describe the classes and methods used in the application. The permanent generation is populated by the JVM at runtime based on classes in use by the application. In addition, Java SE library classes and methods may be stored here.

### The Generational Garbage Collection Process
![copy-referenced-objects](/assets/img/sample/copy-referenced-objects.PNG)
![object-aging](/assets/img/sample/object-aging.PNG)
![additional-aging](/assets/img/sample/additional-aging.PNG)
![promotion](/assets/img/sample/promotion.PNG)

### Java Garbage Collectors
- The Serial GC
With the serial collector, both minor and major garbage collections are done serially (using a single virtual CPU).
-XX:+UseSerialGC
- The Parallel GC
The parallel garbage collector uses multiple threads to perform the young genertion garbage collection. By default on a host with N CPUs, the parallel garbage collector uses N garbage collector threads in the collection.
-XX:+UseParallelGC
-XX:+UseParallelOldGC
- The Concurrent Mark Sweep (CMS) Collector
The Concurrent Mark Sweep (CMS) collector (also referred to as the concurrent low pause collector) collects the tenured generation. It attempts to minimize the pauses due to garbage collection by doing most of the garbage collection work concurrently with the application threads. The CMS collector should be used for applications that require low pause times and can share resources with the garbage collector.
-XX:+UseConcMarkSweepGC
to set the number of threads use:
-XX:ParallelCMSThreads=<n>
- The G1 Garbage Collector
The Garbage First or G1 garbage collector is available in Java 7 and is designed to be the long term replacement for the CMS collector. The G1 collector is a parallel, concurrent, and incrementally compacting low-pause garbage collector.
-XX:+UseG1GC
> Command: java -Xmx12m -Xms3m -Xmn1m -XX:PermSize=20m -XX:MaxPermSize=20m -XX:+UseSerialGC -jar c:\javademos\demo\jfc\Java2D\Java2demo.jar
![](/assets/img/sample/GC-Parameter.png)

