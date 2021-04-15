---
title: Java Collections
date: 2021-02-24 10:30:22 +0800
categories: [java, questions]
tags: [questions]
---

# Collections
[容器详解](https://geek-docs.com/java/java-collection/java-collection-index.html)
- Java有哪些容器?

![](../assets/img/sample/Collection2.png)

![层次图](../assets/img/sample/collection.png)

- Collection 和 Collections 有什么区别？
**java.util.Collection** 是一个集合接口（集合类的一个顶级接口）。它提供了对集合对象进行基本操作的通用接口方法。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式，其直接继承接口有List与Set。
**Collections** 则是集合类的一个工具类/帮助类，其中提供了一系列**静态方法**，用于对集合中元素进行排序、搜索以及线程安全等各种操作。

- comparable 和 Comparator 的区别？[Details](https://www.geeksforgeeks.org/comparable-vs-comparator-in-java/)
A `comparable` object is capable of comparing itself with another object. The class itself must implements the `java.lang.Comparable` interface to compare its instances.

A basic differentiating feature is that using comparable we can use only one comparison. Whereas, we can write more than one custom comparators as you want for a given type, all using different interpretations of what sorting means. Like in the comparable example we could just sort by only one attribute, i.e., year but in the comparator, we were able to use different attributes like rating, name, and year as well.
```java

import java.util.ArrayList;// person对象没有实现Comparable接口，所以必须实现，这样才不会出错，才可以使treemap中的数据按顺序排列
import java.util.Collections;
// 前面一个例子的String类已经默认实现了Comparable接口，详细可以查看String类的API文档，另外其他
// 像Integer类等都已经实现了Comparable接口，所以不需要另外实现了
public class Person implements Comparable<Person> {
    private String name;
    private int age;

    public Person(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    /**
     * T重写compareTo方法实现按年龄来排序
     */
    @Override
    public int compareTo(Person o) {
        if (this.age > o.getAge()) {
            return 1;
        }
        if (this.age < o.getAge()) {
            return -1;
        }
        return 0;
    }
    public static void main(String[] args) {
        TreeMap<Person, String> pdata = new TreeMap<Person, String>();

        pdata.put(new Person("张三", 30), "zhangsan");
        pdata.put(new Person("李四", 20), "lisi");
        pdata.put(new Person("王五", 10), "wangwu");
        pdata.put(new Person("小红", 5), "xiaohong");
        // 得到key的值的同时得到key所对应的值
        Set<Person> keys = pdata.keySet();
        for (Person key : keys) {
            System.out.println(key.getAge() + "-" + key.getName());

        }

        ArrayList<Person> arr = new ArrayList<>();
        Collections.sort(arr);
    }
}
```

```java
//A Java program to demonstrate Comparator interface
import java.io.*;
import java.util.*;

// A class 'Movie' that implements Comparable
class Movie implements Comparable<Movie>
{
	private double rating;
	private String name;
	private int year;

	// Used to sort movies by year
	public int compareTo(Movie m)
	{
		return this.year - m.year;
	}

	// Constructor
	public Movie(String nm, double rt, int yr)
	{
		this.name = nm;
		this.rating = rt;
		this.year = yr;
	}

	// Getter methods for accessing private data
	public double getRating() { return rating; }
	public String getName() { return name; }
	public int getYear()	 { return year; }
}

// Class to compare Movies by ratings
class RatingCompare implements Comparator<Movie>
{
	public int compare(Movie m1, Movie m2)
	{
		if (m1.getRating() < m2.getRating()) return -1;
		if (m1.getRating() > m2.getRating()) return 1;
		else return 0;
	}
}

// Class to compare Movies by name
class NameCompare implements Comparator<Movie>
{
	public int compare(Movie m1, Movie m2)
	{
		return m1.getName().compareTo(m2.getName());
	}
}

// Driver class
class Main
{
	public static void main(String[] args)
	{
		ArrayList<Movie> list = new ArrayList<Movie>();
		list.add(new Movie("Force Awakens", 8.3, 2015));
		list.add(new Movie("Star Wars", 8.7, 1977));
		list.add(new Movie("Empire Strikes Back", 8.8, 1980));
		list.add(new Movie("Return of the Jedi", 8.4, 1983));

		// Sort by rating : (1) Create an object of ratingCompare
		//				 (2) Call Collections.sort
		//				 (3) Print Sorted list
		System.out.println("Sorted by rating");
		RatingCompare ratingCompare = new RatingCompare();
		Collections.sort(list, ratingCompare);
		for (Movie movie: list)
			System.out.println(movie.getRating() + " " +
							movie.getName() + " " +
							movie.getYear());


		// Call overloaded sort method with RatingCompare
		// (Same three steps as above)
		System.out.println("\nSorted by name");
		NameCompare nameCompare = new NameCompare();
		Collections.sort(list, nameCompare);
		for (Movie movie: list)
			System.out.println(movie.getName() + " " +
							movie.getRating() + " " +
							movie.getYear());

		// Uses Comparable to sort by year
		System.out.println("\nSorted by year");
		Collections.sort(list);
		for (Movie movie: list)
			System.out.println(movie.getYear() + " " +
							movie.getRating() + " " +
							movie.getName()+" ");
	}
}

```

- List,Set,Map 三者的区别？
List：存储的元素是有序的、可重复的。
Set: 存储的元素是无序的、不可重复的。
Map: 使用键值对（kye-value）存储，Key 是无序的、不可重复的，value 是无序的、可重复的，每个键最多映射到一个值。

## 集合框架底层数据结构总结
Collection 接口下面的集合:
- List
Arraylist： Object\[]数组， ArrayList内部是拿数组存储，那么大小上限就是Integer.MAX_VALUE： ((2^31)-1)2147483647
> private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
Vector：Object\[]数组，
LinkedList： 双向链表，理论上大小是无限的。

- Set
HashSet（无序，唯一）: 基于 HashMap 实现的，底层采用 HashMap 来保存元素
LinkedHashSet：LinkedHashSet 是 HashSet 的子类，并且其内部是通过 LinkedHashMap 来实现的。有点类似于我们之前说的 LinkedHashMap 其内部是基于 HashMap 实现一样，不过还是有一点点区别的
TreeSet（有序，唯一）： 红黑树(自平衡的排序二叉树)

Map 接口下面的集合:
- Map

![hashmap structure](../assets/img/sample/hashmap.jpg)

HashMap： JDK1.8 之前 HashMap 由数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）。JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间

![LinkedHashMap structure](../assets/img/sample/LinkedHashMap.jpg)

[Details](https://www.imooc.com/article/22931)
LinkedHashMap： LinkedHashMap 继承自 HashMap，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，LinkedHashMap 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。
Hashtable： 同步的。数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的
TreeMap： 红黑树（自平衡的排序二叉树）
> 保证线程安全就选用 ConcurrentHashMap

## List
- Arraylist 和 Vector 的区别?
1. 同步：ArrayList是非同步的，这意味着多个线程可以同时在ArrayList上工作。对于例如如果一个线程正在对ArrayList执行添加操作，则可能有另一个线程在多线程环境中同时对ArrayList执行删除操作
Vector同步。这意味着如果一个线程正在处理Vector，则没有其他线程可以获取它。与ArrayList不同，一次只有一个线程可以对向量执行操作。

2. 调整大小：ArrayList和Vector都可以动态增长和缩小以保持存储的最佳使用，但是它们调整大小的方式是不同的。调整大小时，ArrayList的大小增加一半，而增长时，Vector默认大小增加一倍。

3. 性能：ArrayList提供更好的性能，因为它是非同步的。Vector操作性能较差，因为它们是线程安全的，在Vector上工作的线程会对其进行锁定，这使得其他线程等待直到锁定被释放。

4. 快速失败(fail fast)：首先让我解释什么是快速失败：如果集合（ArrayList，vector等）通过任何方式进行结构修改，在创建迭代器后，迭代器将抛出ConcurrentModificationException。结构修改是指从集合中添加或删除元素。
Vector返回的迭代器不是快速失败的。另一方面，ArrayList返回的iterator和listIterator是快速失​​败的。
[**Fail-Fast iterators**](https://www.geeksforgeeks.org/fail-fast-fail-safe-iterators-java/) immediately throw ConcurrentModificationException if there is structural modification of the collection. Structural modification means adding, removing any element from collection while a thread is iterating over that collection. Iterator on ArrayList, HashMap classes are some examples of fail-fast Iterator.
**Fail-Safe iterators** don’t throw any exceptions if a collection is structurally modified while iterating over it. This is because, they operate on the clone of the collection, not on the original collection and that’s why they are called fail-safe iterators. Iterator on CopyOnWriteArrayList, ConcurrentHashMap classes are examples of fail-safe Iterator.

- Arraylist 与 LinkedList 区别?
1. 底层数据结构： Arraylist 底层使用的是 Object 数组；LinkedList 底层使用的是 双向链表 数据结构
2. 搜索：与LinkedList搜索操作相比，ArrayList支持快速随机访问，搜索操作非常快。ArrayList中的get(int index)给出O(1)的性能，而LinkedList性能为O(n)。
原因：ArrayList维护其元素的基于索引的系统，因为它隐式使用数组数据结构，这使得搜索列表中的元素更快。另一方面，LinkedList实现了双向链表，它需要遍历所有元素来搜索元素。

3. 删除 & 插入元素：LinkedList删除插入操作给出O(1)性能，而ArrayList提供可变性能：在最坏的情况下O(n)（删除第一个元素）和在最好的情况下O(1)（删除最后一个元素）。
结论：与ArrayList相比，LinkedList元素删除和添加元素更快。
原因：LinkedList的每个元素都维护着两个指针（地址），这些指针指向列表中的两个邻居元素。因此，移除仅需要改变将要移除的节点的两个相邻节点（元素）中的指针位置。在ArrayList中，需要移动所有元素以填充由要移除的元素创建的空间。

4. 内存开销：ArrayList维护索引和元素数据，而LinkedList维护元素数据和相邻节点的两个指针，因此LinkedList中的内存消耗比较高

- Array 和 ArrayList 有何区别？
1. Array可以容纳基本类型和对象，而ArrayList只能容纳对象。
2. Array是指定大小的，而ArrayList大小是固定的。
3. Array没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。

### ArrayList 扩容机制
[](https://www.cnblogs.com/kuoAT/p/6771653.html)
[源码解析](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ArrayList%E6%BA%90%E7%A0%81+%E6%89%A9%E5%AE%B9%E6%9C%BA%E5%88%B6%E5%88%86%E6%9E%90?id=_2-arraylist-%e6%a0%b8%e5%bf%83%e6%ba%90%e7%a0%81%e8%a7%a3%e8%af%bb)
分析过程：add() ---> ensureCapacityInternal(int minCapacity) ---> ensureExplicitCapacity(int minCapacity)  ---> grow(int minCapacity)
minCapacity：向ArrayList插入元素所需的最小容量。

## Set
- 无序性 & 不可重复性
无序性：不等于随机性。存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据数组的哈希值决定的
不可重复性：保证添加的元素按照equals（）判断时，不能返回true，即相同的元素只能添加一个。

- 比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同
HashSet 是 Set 接口的主要实现类 ，HashSet 的底层是 HashMap，线程不安全的，可以存储 null 值；
LinkedHashSet 是 HashSet 的子类，能够按照元素添加的顺序遍历；
TreeSet 底层使用红黑树，能够对其中的元素进行排序，排序的方式有自然排序和定制排序。

- HashSet 如何检查重复?
hashcode() ---> equals()

## Map
- [HashMap源码解析](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/HashMap(JDK1.8)%E6%BA%90%E7%A0%81+%E5%BA%95%E5%B1%82%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E5%88%86%E6%9E%90.md)
- [Hashmap实现原理及扩容机制详解](https://blog.csdn.net/lkforce/article/details/89521318)
HashMap Key Words:
1. Initial Capacity – It is the capacity of HashMap at the time of its creation (It is the number of buckets a HashMap can hold when the HashMap is instantiated). In java, it is 2^4=16 initially, meaning it can hold 16 key-value pairs.
2. Load Factor – It is the percent value of the capacity after which the capacity of Hashmap is to be increased (It is the percentage fill of buckets after which Rehashing takes place). In java, it is 0.75f by default, meaning the rehashing takes place after filling 75% of the capacity.
3. Threshold – It is the product of Load Factor and Initial Capacity. In java, by default, it is (16 * 0.75 = 12). That is, Rehashing takes place after inserting 12 key-value pairs into the HashMap.
4. Rehashing – It is the process of doubling the capacity of the HashMap after it reaches its Threshold. In java, HashMap continues to rehash(by default) in the following sequence – 2^4, 2^5, 2^6, 2^7, …. so on.
-  HashMap 的 hash 扰动函数刨析：[Details](https://www.zhihu.com/question/20733617)
```
static final int hash(Object key) {
      int h;
      // key.hashCode()：返回散列值也就是hashcode
      // ^ ：按位异或
      // >>>:无符号右移，忽略符号位，空位都以0补齐
      return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  //  因为二进制的32位带符号的int值的范围在(-2^31 ~ 2^31-1), 而HashMap的初始容量才为16，因此 key.hashCode()的值不可直接作为key，需要取模（indexFor）
}
```

![hash](../assets/img/sample/hash.png)

```
bucketIndex = indexFor(hash, table.length);
static int indexFor(int h, int length) {
     return h & (length-1);  //取模运算， 若length == 2^4， 则该操作可以截取hashcode的低四位作为key。因此length必须为2的幂次.
}
```

![hashcode](../assets/img/sample/hashcode.png)


- HashMap 和 Hashtable 的区别
线程是否安全： HashMap 是非线程安全的，HashTable 是线程安全的,因为 HashTable 内部的方法基本都经过synchronized 修饰。（如果要保证线程安全的话就使用 ConcurrentHashMap）；
效率： 因为线程安全的问题，HashMap 要比 HashTable 效率高一点。
对 Null key 和 Null value 的支持： HashMap 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个；HashTable 不允许有 null 键和 null 值，否则会抛出 NullPointerException。
初始容量大小和每次扩充容量大小的不同 ： ① 创建时如果不指定容量初始值，Hashtable 默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。HashMap 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。② 创建时如果给定了容量初始值，那么 Hashtable 会直接使用你给定的大小，而 HashMap 会将其扩充为 2 的幂次方大小（HashMap 中的tableSizeFor()方法保证，下面给出了源代码）。也就是说 HashMap 总是使用 2 的幂作为哈希表的大小。
底层数据结构： JDK1.8 以后的 HashMap 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。
```
// tableSizeFor(int cap)是hashMap中的一个Static函数，主要功能是返回一个比给定整数大且最接近的2的幂次方整数，如给定7，返回2的3次方8
/**
     * Returns a power of two size for the given target capacity.
     */
    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;    // 无符号右移运算符: 符号右移运算符和右移运算符( >> )是一样的，不过无符号右移运算符在右移的时候是补0的，而右移运算符是补符号位的.
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

- HashMap 和 HashSet 区别
HashSet is implementation of Set Interface which does not allow duplicate value. The main thing is, objects that are stored in HashSet must override equals() for check for equality and hashCode() methods for no duplicate value are stored in our set.
HashMap is an implementation of Map Interface, which map a key to value. Duplicate keys are not allowed in a map.

![differences](../assets/img/sample/hashset-hashmap.png)

- HashMap vs TreeMap [Details](https://www.baeldung.com/java-treemap-vs-hashmap)

![map hierarchy](../assets/img/sample/map.png)

1. Implementation: both TreeMap and HashMap extend `AbstractMap`, except that TreeMap also implements NavigableMap interface, SortedMap interface. A TreeMap stores map elements in a Red-Black tree, which is a Self-Balancing Binary Search Tree. A HashMap works on the principle of `hashing`.
2. Order : items in a TreeMap are sorted according to their natural order. HashMap doesn't provide any guarantee over the way the elements are arranged in the Map.
3. Null Values : HashMap allows storing at most one null key and many null values. TreeMap doesn't allow a null key but may contain many null values.
4. Performance Analysis: HashMap provides expected constant-time performance O(1) for most operations like add(), remove() and contains(). Therefore, it's significantly faster than a TreeMap. TreeMap provides a performance of O(log(n)) for most operations like add(), remove() and contains().

- Collision Handling
1. Chaining (链表法): 链表法就是将相同hash值的对象组织成一个链表放在hash值对应的槽位
2. Open Addressing[Details](https://www.geeksforgeeks.org/hashing-set-3-open-addressing/) : 开放地址法是通过一个探测算法，当某个槽位已经被占据的情况下继续查找下一个可以使用的槽位。
   // S is hashtable size
   - Linear Probing: hash(x) % S is full --try--> (hash(x) + 1) % S --try--> (hash(x) + 2) % S
   - Quadratic Probing: hash(x) % S --try--> (hash(x) + 1\*1) % S --try--> (hash(x) + 2*2) % S
   - Double Hashing: hash(x) % S --try--> (hash(x) + 1\*hash2(x)) % S --try--> (hash(x) + 2*hash2(x)) % S

- HashMap 7中遍历方式 [Source](https://mp.weixin.qq.com/s/Zz6mofCtmYpABDL1ap04ow)
```
Map<Integer, String> map = new HashMap();
1.
Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry<Integer, String> entry = iterator.next();
            System.out.print(entry.getKey());
            System.out.print(entry.getValue());
}
2.
 Iterator<Integer> iterator = map.keySet().iterator();
        while (iterator.hasNext()) {
            Integer key = iterator.next();
            System.out.print(key);
            System.out.print(map.get(key));
}
3.
for (Map.Entry<Integer, String> entry : map.entrySet()) {
            System.out.print(entry.getKey());
            System.out.print(entry.getValue());
}
4.
for (Integer key : map.keySet()) {
            System.out.print(key);
            System.out.print(map.get(key));
}
5. Lambda
map.forEach((key, value) -> {
            System.out.print(key);
            System.out.print(value);
});
```

- ConcurrentHashMap
[源码分析](https://crossoverjie.top/2018/07/23/java-senior/ConcurrentHashMap/)
[ConcurrentHashMap1.8 - 扩容详解](https://blog.csdn.net/ZOKEKAI/article/details/90051567)
是线程安全的。
- ConcurrentHashMap 和 Hashtable 的区别
底层数据结构： JDK1.7 的 ConcurrentHashMap 底层采用 分段的数组+链表 实现，JDK1.8 采用的数据结构跟 HashMap1.8 的结构一样，数组+链表/红黑二叉树。Hashtable 和 JDK1.8 之前的 HashMap 的底层数据结构类似都是采用 数组+链表 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
实现线程安全的方式： ① 在 JDK1.7 的时候，ConcurrentHashMap（分段锁） 对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 到了 JDK1.8 的时候已经摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。（JDK1.6 以后 对 synchronized 锁做了很多优化） 整个看起来就像是优化过且线程安全的 HashMap，虽然在 JDK1.8 中还能看到 Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；② Hashtable(同一把锁) :使用 synchronized 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。

## Collections 工具类
- **排序**
```
void reverse(List list)//反转
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
```

- **查找,替换操作**
```
int binarySearch(List list, Object key)//对List进行二分查找，返回索引，注意List必须是有序的
int max(Collection coll)//根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
int max(Collection coll, Comparator c)//根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
void fill(List list, Object obj)//用指定的元素代替指定list中的所有元素。
int frequency(Collection c, Object o)//统计元素出现次数
int indexOfSubList(List list, List target)//统计target在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target).
boolean replaceAll(List list, Object oldVal, Object newVal), 用新元素替换旧元素
```
- **同步控制**(不推荐，需要线程安全的集合类型时请考虑使用 JUC 包下的并发集合)
Collections 提供了多个synchronizedXxx()方法·，该方法可以将指定集合包装成线程同步的集合，从而解决多线程并发访问集合时的线程安全问题。
 HashSet，TreeSet，ArrayList,LinkedList,HashMap,TreeMap 都是线程不安全的。Collections 提供了多个静态方法可以把他们包装成线程同步的集合。
