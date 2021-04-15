---
title: Java Basic Q&A
date: 2021-02-22 15:30:22 +0800
categories: [java, questions]
tags: [questions]
---

# Java 基础概念
1. **Java的语言特性**
- **面向对象（封装，继承，多态**
- **[平台无关性](https://segmentfault.com/a/1190000021898950)**（由Java虚拟机实现: Java通过规定Java语言中基本数据类型的取值范围和行为，统一将Java文件编译成Class文件（字节码文件），并且最后由Java虚拟机将Class文件转换成对应平台的二进制文件，才实现了平台无关性。）

![Java编译过程](/assets/img/sample/java-compile.png)

编译型语言（C，C++）是指编译器针对特定的操作系统将源代码一次性翻译成可被该平台执行的机器码；解释型语言（Java, C#）是指解释器对源程序逐行解释成特定平台的机器码并立即执行。
> 在 Java 中，JVM 可以理解的代码就叫做字节码（即扩展名为 .class 的文件），
> 它不面向任何特定的处理器，只面向虚拟机。Java 语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。
> 所以 Java 程序运行时比较高效，而且，由于字节码并不针对一种特定的机器，因此，Java 程序无须重新编译便可在多种不同操作系统的计算机上运行。
- **支持多线程**
- **支持网络编程且方便** ：在Java语言中，对于TCP方式的网络编程提供了良好的支持，在实际实现时，以java.net.Socket类代表客户端连接，以java.net.ServerSocket类代表服务器端连接。
在进行网络编程时，底层网络通讯的细节已经实现了比较高的封装，所以在程序员实际编程时，只需要指定IP地址和端口号码就可以建立连接了。正是由于这种高度的封装，一方面简化了Java语言网络编程的难度，另外也使得使用Java语言进行网络编程时无法深入到网络的底层。
- **编译与解释并存**
- **可靠性**
- **安全性**
2. **JDK & JRE 区别**
- JDK :Java Development Kit ，它是功能齐全的 Java SDK。它拥有 JRE 所拥有的一切，还有编译器（javac）和工具（jdb（调试程序） javadoc（文档生成器））。它能够创建和编译程序。
- JRE : Java Runtime Environment。它是运行已编译 Java 程序所需的所有内容的集合，包括 Java 虚拟机（JVM），Java 类库，java 命令和其他的一些基础构件。但是，它不能用于创建新程序。

3. **Java 和 C++的区别**
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
- Java 不提供指针来直接访问内存，程序内存更加安全。
- Java 有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存。
- 都是面向对象的语言，都支持封装、继承和多态

# Java语法
1. **基本类型占存储空间的大小**

![基本类型](/assets/img/sample/basic-variable-size.jpg)

2. **标识符和关键字**
标识符（Identifier）是用户编程时使用的名字，用于给变量、常量、函数、语句块等命名，以建立起名称与使用之间的关系。
关键字（Keywords）是由Java语言规定的具有特定意义的字符串，通常也称为保留字。我们定义的标识符不能与关键字相同，否则会出现错误。
3. **public, protected, private**

![](/assets/img/sample/identifier.png)

4. **Java 泛型了解么？什么是类型擦除？介绍一下常用的通配符？**
**[Java泛型](https://www.baeldung.com/java-generics)**是在JDK 5.0中引入的，目的是减少bug并在类型上增加额外的抽象层。
Java的泛型是伪泛型，这是因为Java在编译期间，所有的泛型信息都会被擦掉。
Java的泛型基本上都是在编译器这个层次上实现的，在生成的字节码中是不包含泛型中的类型信息的，使用泛型的时候加上类型参数，这个过程成为[**类型擦除**](https://www.cnblogs.com/wuqinglong/p/9456193.html)。
常用通配符为： T，E，K，V，?
- ? 表示不确定的 java类型
- T (type) 表示具体的一个 java 类型
- K V (key value) 分别代表 java 键值中的 Key Value
- E (element) 代表 Element
4. **== 和 equals 的区别**
- == : 它的作用是判断两个对象的地址是不是相等。即判断两个对象是不是同一个对象。(基本数据类型==比较的是值，引用数据类型==比较的是内存地址)
- equals() : 它的作用也是判断两个对象是否相等，它不能用于比较基本数据类型的变量。equals()方法存在于Object类中，而Object类是所有类的直接或间接父类。
Object类equals()方法：
```
public boolean equals(Object obj) {
     return (this == obj);
}
```
**equals() 方法存在两种使用情况：**
情况 1：类没有覆盖 equals()方法。则通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象。使用的默认是 Object类equals()方法。
情况 2：类覆盖了 equals()方法。一般，我们都覆盖 equals()方法来两个对象的内容相等；若它们的内容相等，则返回 true(即，认为这两个对象相等)。
Note: 当创建 String 类型的对象时，虚拟机会在**常量池**中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 String 对象。

5. **为什么要有 hashCode？**[(Ref)](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/basis/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md)
hashCode() 的作用是获取哈希码，也称为散列码；这个哈希码的作用是确定该对象在哈希表中的索引位置。(返回的是int整数)
hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode() 函数。hashCode() 在需要用到散列表的类中才有用，如：HashMap，Hashtable，HashSet在其它情况下没用.
加快检索速度：例如：当把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，
如果没有相符的 hashcode，HashSet 会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用 equals() 方法来检查 hashcode 相等的对象是否真的相同。
如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样就大大减少了 equals 的次数，相应就大大提高了执行速度。

6. **为什么重写 equals 时必须重写 hashCode 方法？**[(Ref)](https://www.cnblogs.com/skywang12345/p/3324958.html)
- 如果两个对象相等，那么它们的hashCode()值一定相同。（这里的相等是指，通过equals()比较两个对象时返回true。）
- 如果两个对象hashCode()相等，它们并不一定相等。（因为在散列表中，hashCode()相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。）
- 在这种情况下。若要判断两个对象是否相等，除了要覆盖equals()之外，也要覆盖hashCode()函数。否则，equals()无效。
> hashCode()的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

# Java基本数据类型
1. **Java有8种基本数据类型**
- 6 种数字类型 ：byte、short、int、long、float、double
- 1 种字符类型：char
- 1 种布尔型：boolean。
这八种基本类型都有对应的包装类分别为：Byte、Short、Integer、Long、Float、Double、Character、Boolean
Note : Java 里使用 long 类型的数据一定要在数值后面加上 L，否则将作为整型解析：
2. **装箱&拆箱**  [Ref](https://www.cnblogs.com/dolphin0520/p/3780005.html)
**装箱**：自动将基本数据类型转换为包装器类型
**拆箱**：自动将包装器类型转换为基本数据类型。
> Integer i = new Integer(10);  // 一般情况， 每次都会创建新的Integer对象
>
> Integer i = 10;   // 装箱  在装箱的时候自动调用的是Integer的valueOf(int)方法。
>
> int n = i;    // 拆箱   而在拆箱的时候自动调用的是Integer的intValue方法。

```
public static Integer valueOf(int i) {
        if(i >= -128 && i <= IntegerCache.high)
            return IntegerCache.cache[i + 128];
        else
            return new Integer(i);
}

public class Main {
    public static void main(String[] args) {
        Integer i1 = 40;    // 装箱，使用常量池中的对象。
        Integer i2 = new Integer(40);  //创建新对象
        System.out.println(i1 == i2);// false

        Double i1 = 100.0;   // 没有实现常量池技术，没有缓存
        Double i2 = 100.0;
        Double i3 = 200.0;
        Double i4 = 200.0;

        System.out.println(i1==i2);   // true
        System.out.println(i3==i4);   // false
    }
}
```

在通过valueOf方法创建Integer对象的时候，如果数值在\[-128,127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用；否则创建一个新的Integer对象。
Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的。
Double、Float的valueOf方法的实现是类似的。

```
public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
}

public class Main {
    public static void main(String[] args) {

        Boolean i1 = false;
        Boolean i2 = false;

        System.out.println(i1==i2);   // true
    }
}
```

3. 8 种基本类型的包装类和常量池
Java 基本类型的包装类的大部分都实现了常量池技术，即 Byte,Short,Integer,Long,Character,Boolean；
前面 4 种包装类默认创建了数值\[-128，127] 的相应类型的缓存数据，Character 创建了数值在\[0,127]范围的缓存数据，Boolean 直接返回 True Or False。如果超出对应范围仍然会去创建新的对象。
两种浮点数类型的包装类 Float,Double 并没有实现常量池技术。

# Java 类
## Loading, Linking, and Initializing
[Offical Document](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-5.html)
The Java Virtual Machine dynamically loads, links and initializes classes and interfaces. Loading is the process of finding the binary representation of a class or interface type with a particular name and creating a class or interface from that binary representation. Linking is the process of taking a class or interface and combining it into the run-time state of the Java Virtual Machine so that it can be executed. Initialization of a class or interface consists of executing the class or interface initialization method

### Java Class Loaders
Class loaders are responsible for loading Java classes during runtime dynamically to the JVM (Java Virtual Machine). Also, they are part of the JRE (Java Runtime Environment). Hence, the JVM doesn't need to know about the underlying files or file systems in order to run Java programs thanks to class loaders.
Also, these Java classes aren't loaded into memory all at once, but when required by an application.
[Three Types of Java Classloaders:](https://www.geeksforgeeks.org/classloader-in-java/)
- BootStrap ClassLoader
- Extension ClassLoader
- Application ClassLoader(System ClassLoader)

Delegation Model:

![](/assets/img/sample/java-classloader.png)

[Custom Classloader](https://www.baeldung.com/java-classloaders)

## Basic Question
1. 值的传递方式
按值调用(call by value)表示方法接收的是调用者提供的值，  而按引用调用（call by reference表示方法接收的是调用者提供的变量地址。一个函数可以修改传递引用所对应的变量值，而不能修改传递值调用所对应的变量值。
Java总是采用按值调用。也就是说，方法得到的是所有参数值的一个拷贝，也就是说，方法不能修改传递给它的任何参数变量的内容。
一个方法不能修改一个基本数据类型的参数，而对象引用作为参数就不一样

```
public static void main(String[] args) {
		int[] arr = { 1, 2, 3, 4, 5 };
		System.out.println(arr[0]);   // 1
		change(arr);
		System.out.println(arr[0]);   // 0
	}
public static void change(int[] array) {   // 拷贝的是引用，指向同一数组
		// 将数组的第一个元素变为0
		array[0] = 0;
	}
```

```
public class Test {
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Student s1 = new Student("小张");
		Student s2 = new Student("小李");
		Test.swap(s1, s2);
		System.out.println("s1:" + s1.getName());  // 小张
		System.out.println("s2:" + s2.getName());  // 小李
	}

	public static void swap(Student x, Student y) {
		Student temp = x;
		x = y;
		y = temp;
		System.out.println("x:" + x.getName());  // 小李
		System.out.println("y:" + y.getName());  // 小张
	}
}
```
方法并没有改变存储在变量 s1 和 s2 中的对象引用。swap 方法的参数 x 和 y 被初始化为两个对象引用的拷贝，这个方法交换的是这两个拷贝。

2. **重载和重写的区别？**
当一个类中的两个或多个方法具有相同的方法名但不同的参数时，就会发生**重载**。
**重写**意味着拥有两个具有相同方法名称和参数的方法(即方法签名)。其中一个方法在父类中，另一个在子类中。重写允许子类提供其父类已经提供的方法的特定实现。
Here are some important facts about Overriding and Overloading: [Ref](https://www.programcreek.com/2009/02/overriding-and-overloading-in-java-with-examples/)
1). The real object type in the run-time, not the reference variable's type, determines which overridden method is used at runtime.
In contrast, reference type determines which overloaded method will be used at compile time.
2). Polymorphism applies to overriding, not to overloading.
3). Overriding is a run-time concept while overloading is a compile-time concept.

**Overriding**
```
class Dog{
    public void bark(){
        System.out.println("woof ");
    }
}
class Hound extends Dog{
    public void sniff(){
        System.out.println("sniff ");
    }

    public void bark(){
        System.out.println("bowl");
    }
}

public class OverridingTest{
    public static void main(String [] args){
        Dog dog = new Hound();
        dog.bark();  // 调用Hound类的bark方法
    }
}
```
**Overloading**
```
class Dog{
    public void bark(){
        System.out.println("woof ");
    }

    //overloading method
    public void bark(int num){
    	for(int i=0; i<num; i++)
    		System.out.println("woof ");
    }
}
```
![重载](/assets/img/sample/overload.jpg)

3. 深拷贝 vs 浅拷贝
**浅拷贝**：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝。
**深拷贝**：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容。

4. 普通类和抽象类有哪些区别？
- 普通类不能包含抽象方法，抽象类可以包含抽象方法。
- 抽象类不能直接实例化，普通类可以直接实例化。

5. 接口和抽象类有什么区别？[Source](https://www.geeksforgeeks.org/difference-between-abstract-class-and-interface-in-java/)

- Type of methods: Interface can have only abstract methods. Abstract class can have abstract and non-abstract methods. From Java 8, it can have default and static methods also.
- Final Variables: Variables declared in a Java interface are by default final. An abstract class may contain non-final variables.
- Type of variables: Abstract class can have final, non-final, static and non-static variables. Interface has only static and final variables. : public static final
- Implementation: Abstract class can provide the implementation of interface. Interface can’t provide the implementation of abstract class.
- Inheritance vs Abstraction: A Java interface can be implemented using keyword “implements” and abstract class can be extended using keyword “extends”.
- Multiple implementation: An interface can extend another Java interface only, an abstract class can extend another Java class and implement multiple Java interfaces.
- Accessibility of Data Members: Members of a Java interface are public by default. A Java abstract class can have class members like private, protected, etc.


# Java面向对象
1. **面向对象&面向过程**
[Differences between Procedural and Object Oriented Programming](https://www.geeksforgeeks.org/differences-between-procedural-and-object-oriented-programming/)
Characteristics of an Object Oriented Programming language

![Characteristics of an Object Oriented Programming language](/assets/img/sample/OOPs-Concepts.jpg)

2. **面向对象特征** [Ref](https://www.careerride.com/oops-characteristics.aspx)
- **封装（Encapsulation）**
Binding data and operations of data together in a single unit – A class adhere this feature.
Encapsulation is capturing data and keeping it safely and securely from outside interfaces.

- **继承（Inheritance）**
This is the process by which a class can be derived from a base class with all features of base class, and some of its own.
These increases code reusability and extension of existing classes.

- **多态（Polymorphism）**
Multiple definitions for a single name - functions with same name with different functionality; saves time in investing many function names Operator and Function overloading

- **抽象（Abstraction）**
Specifying what to do but not how to do ; a flexible feature for having a overall view of an object’s functionality.
it represents data at a very conceptual level without any details.

3. **构造器 Constructor 是否可被 override?**
Constructor 不能被 override（重写）,但是可以 overload（重载）,所以一个类中可以有多个构造函数的情况。
**2.1 No-argument constructor & Parameterized Constructor** [Ref](https://www.geeksforgeeks.org/constructors-in-java/)
No-argument constructor: A constructor that has no parameter is known as default constructor.
If we don’t define a constructor in a class, then compiler creates default constructor(with no arguments) for the class.
And if we write a constructor with arguments or no-arguments then the compiler does not create a default constructor.
Default constructor provides the default values to the object like 0, null, etc. depending on the type.
**2.2 Constructor Chaining** [Ref](https://www.geeksforgeeks.org/constructor-chaining-java-examples/)
Within same class: It can be done using this() keyword for constructors in same class
From base class: by using super() keyword to call constructor from the base class.

4. **在一个静态方法内调用一个非静态成员为什么是非法的?**
由于静态方法可以不通过对象进行调用，因此在静态方法里，不能调用其他非静态变量，也不可以访问非静态变量成员。
Reason: 你不能调用不存在的东西。因为还没有创建对象，所以这个非静态方法还不存在。静态方法(根据定义)始终存在。

5. **静态方法和实例方法有何不同**
调用静态方法可以无需创建对象。在外部调用静态方法时，可以使用"类名.方法名"的方式，也可以使用"对象名.方法名"的方式。而实例方法只有后面这种方式。
静态方法在访问本类的成员时，只允许访问静态成员（即静态成员变量和静态方法），而不允许访问实例成员变量和实例方法；实例方法则无此限制。

6. **成员变量与局部变量的区别有哪些？**
从**语法形式**上看:成员变量是属于类的，而局部变量是在代码块或方法中定义的变量或是方法的参数；成员变量可以被 public,private,static 等修饰符所修饰，而局部变量不能被访问控制修饰符及 static 所修饰；但是，成员变量和局部变量都能被 final 所修饰。
从变量在内存中的**存储方式**来看:如果成员变量是使用static修饰的，那么这个成员变量是属于类的，如果没有使用static修饰，这个成员变量是属于实例的。而对象存在于堆内存，局部变量则存在于栈内存。
从变量在内存中的**生存时间**上看:成员变量是对象的一部分，它随着对象的创建而存在，而局部变量随着方法的调用而自动消失。
成员变量如果没有被**赋初值**:则会自动以类型的默认值而赋值（一种情况例外）被 final 修饰的成员变量也必须显式地赋值），而局部变量则不会自动赋值。

7. **创建一个对象用什么运算符?对象实体与对象引用有何不同?**
new 运算符，new 创建对象实例（对象实例在堆内存中），对象引用指向对象实例（对象引用存放在栈内存中）。一个对象引用可以指向 0 个或 1 个对象;一个对象可以有 n 个引用指向它。

8. **构造方法有哪些特性？**
- 名字与类名相同。
- 没有返回值，不能用 void 作为返回值。
- 生成类的对象时自动执行，无需调用。

7. **在调用子类构造方法之前会先调用父类没有参数的构造方法,其目的是?**
帮助子类做初始化工作。

```
public MountainBike(int startHeight,
                    int startCadence,
                    int startSpeed,
                    int startGear) {
    super(startCadence, startSpeed, startGear);
    seatHeight = startHeight;
}
```
[Ref: Java Tutorials](https://docs.oracle.com/javase/tutorial/java/IandI/super.html)
Invocation of a superclass constructor must be the first line in the subclass constructor.
The syntax for calling a superclass constructor is
super();
or:
super(parameter list);
With super(), the superclass no-argument constructor is called. With super(parameter list), the superclass constructor with a matching parameter list is called.
> Note: If a constructor does not explicitly invoke a superclass constructor, the Java compiler automatically inserts a call to the no-argument constructor of the superclass.
> If the super class does not have a no-argument constructor, you will get a compile-time error. Object does have such a constructor, so if Object is the only superclass, there is no problem.

8. **对象的相等与指向他们的引用相等,两者有什么不同?**
对象的相等，比的是内存中存放的内容是否相等。而引用相等，比较的是他们指向的内存地址是否相等。

# 其他
1. String StringBuffer 和 StringBuilder 的区别是什么? String 为什么是不可变的?
简答：
String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象，
而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。
StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，
但 StringBuilder 的性能却高于 StringBuffer，所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

Details:
\[String 类中使用 final 关键字修饰字符数组来保存字符串，private final char value\[]，所以String 对象是不可变的。
而 StringBuilder 与 StringBuffer 都继承自 AbstractStringBuilder 类，在 AbstractStringBuilder 中也是使用字符数组保存字符串char\[]value 但是没有用 final 关键字修饰，所以这两种对象都是可变的。]
- **线程安全性**
String 中的对象是不可变的，也就可以理解为常量，线程安全。AbstractStringBuilder 是 StringBuilder 与 StringBuffer 的公共父类，定义了一些字符串的基本操作，
如 expandCapacity、append、insert、indexOf 等公共方法。StringBuffer 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。StringBuilder 并没有对方法进行加同步锁，所以是非线程安全的。

2. BIO,NIO,AIO 有什么区别? [Source](https://topic.alibabacloud.com/a/the-difference-between-the-bionioaio_8_8_20242519.html)
BIO(Blocking I/O): The thread initiates an IO request, and the thread blocks until the operation completes, regardless of whether the kernel is ready for IO operation. The following figure:
NIO (Non-blocking/New I/O): Thread initiates IO request, returns immediately; After the kernel is ready for IO operation, the thread begins to block until the operation completes by calling the registered callback function to notify the threads to do IO operations.
（同步非阻塞需要一个线程去轮询所有IO操作的状态改变，在相应的状态改变后，系统会通知对应的线程来处理。）
AIO (Asynchronous I/O): Thread initiates IO request, returns immediately; memory completes IO operation after preparation, do IO operation, until the operation completes or fails, by calling the registered callback function to notify the thread do IO operation or failure.
2.1 同步 & 异步 [Source](https://zhuanlan.zhihu.com/p/83597838)
2.2 阻塞 & 非阻塞
# 反射机制(Reflection)
[Java Tutorial---The Reflection API](https://docs.oracle.com/javase/tutorial/reflect/index.html)
[Demo](https://www.geeksforgeeks.org/reflection-in-java/)
Reflection gives us information about the class to which an object belongs and also the methods of that class which can be executed by using the object.
**Advantages of Using Reflection**:
**Extensibility Features**: An application may make use of external, user-defined classes by creating instances of extensibility objects using their fully-qualified names.
**Debugging and testing tools**: Debuggers use the property of reflection to examine private members on classes.

**Drawbacks:**
**Performance Overhead**: Reflective operations have slower performance than their non-reflective counterparts, and should be avoided in sections of code which are called frequently in performance-sensitive applications.
**Exposure of Internals**: Reflective code breaks abstractions and therefore may change behavior with upgrades of the platform.
```java
// A simple Java program to demonstrate the use of reflection
import java.lang.reflect.Method;
import java.lang.reflect.Field;
import java.lang.reflect.Constructor;

// class whose object is to be created
class Test
{
	// creating a private field
	private String s;

	// creating a public constructor
	public Test() { s = "GeeksforGeeks"; }

	// Creating a public method with no arguments
	public void method1() {
		System.out.println("The string is " + s);
	}

	// Creating a public method with int as argument
	public void method2(int n) {
		System.out.println("The number is " + n);
	}

	// creating a private method
	private void method3() {
		System.out.println("Private method invoked");
	}
}

class Demo
{
	public static void main(String args[]) throws Exception
	{
		// Creating object whose property is to be checked
		Test obj = new Test();

		// Creating class object from the object using
		// getclass method
		Class cls = obj.getClass();
		System.out.println("The name of class is " +
							cls.getName());

		// Getting the constructor of the class through the
		// object of the class
		Constructor constructor = cls.getConstructor();
		System.out.println("The name of constructor is " +
							constructor.getName());

		System.out.println("The public methods of class are : ");

		// Getting methods of the class through the object
		// of the class by using getMethods
		Method[] methods = cls.getMethods();

		// Printing method names
		for (Method method:methods)
			System.out.println(method.getName());

		// creates object of desired method by providing the
		// method name and parameter class as arguments to
		// the getDeclaredMethod
		Method methodcall1 = cls.getDeclaredMethod("method2",
												int.class);

		// invokes the method at runtime
		methodcall1.invoke(obj, 19);

		// creates object of the desired field by providing
		// the name of field as argument to the
		// getDeclaredField method
		Field field = cls.getDeclaredField("s");

		// allows the object to access the field irrespective
		// of the access specifier used with the field
		field.setAccessible(true);

		// takes object and the new value to be assigned
		// to the field as arguments
		field.set(obj, "JAVA");

		// Creates object of desired method by providing the
		// method name as argument to the getDeclaredMethod
		Method methodcall2 = cls.getDeclaredMethod("method1");

		// invokes the method at runtime
		methodcall2.invoke(obj);

		// Creates object of the desired method by providing
		// the name of method as argument to the
		// getDeclaredMethod method
		Method methodcall3 = cls.getDeclaredMethod("method3");

		// allows the object to access the method irrespective
		// of the access specifier used with the method
		methodcall3.setAccessible(true);

		// invokes the method at runtime
		methodcall3.invoke(obj);
	}
}

```

# 异常
1. 异常类层次结构图

![异常类层次结构图](/assets/img/sample/Exception.png)

- Exception :程序本身可以处理的异常，可以通过 catch 来进行捕获。Exception 又可以分为 受检查异常(必须处理, 不处理没法通过编译) 和 不受检查异常(可以不处理，不处理也可通过编译)。
- Error ：Error 属于程序无法处理的错误 ，我们没办法通过 catch 来进行捕获 。例如，
Java 虚拟机运行错误（Virtual MachineError）、虚拟机内存不够错误(OutOfMemoryError)、类定义错误（NoClassDefFoundError）等 。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。
2.  Throwable 类常用方法
- public string getMessage():返回异常发生时的简要描述
- public string toString():返回异常发生时的详细信息
- public string getLocalizedMessage():返回异常对象的本地化信息。使用 Throwable 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 getMessage（）返回的结果相同
- public void printStackTrace():在控制台上打印 Throwable 对象封装的异常信息
3.  try-catch-finally & try-finally
try块： 用于捕获异常。其后可接零个或多个 catch 块，如果没有 catch 块，则必须跟一个 finally 块。
catch块： 用于处理 try 捕获到的异常。
finally 块： 无论是否捕获或处理异常，finally 块里的语句都会被执行。当在 try 块或 catch 块中遇到 return 语句时，finally 语句块将在方法返回之前被执行。
try-catch-finally : [Three cases demos](https://www.geeksforgeeks.org/flow-control-in-try-catch-finally-in-java/)
try-finally : Two cases demos
4. replace `try-catch-finally` with `try-with-resources` [Ref](https://www.baeldung.com/java-try-with-resources)
Support for try-with-resources – introduced in Java 7 – allows us to declare resources to be used in a try block with the assurance that the resources will be closed when after the execution of that block.
```
Scanner scanner = null;
try {
    scanner = new Scanner(new File("test.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}
```
```
try (Scanner scanner = new Scanner(new File("test.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException fnfe) {
    fnfe.printStackTrace();
}
```
```
try (Scanner scanner = new Scanner(new File("testRead.txt"));
    PrintWriter writer = new PrintWriter(new File("testWrite.txt"))) {  // separate declared multiply resources with a semicolon(;).
    while (scanner.hasNext()) {
	writer.print(scanner.nextLine());
    }
}
```
