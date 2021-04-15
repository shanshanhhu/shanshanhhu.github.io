---
title: Java Notes
date: 2021-02-23 21:11:22 +0800
categories: [java, notes]
tags: [notes]
---

# Collections
## ArrayList Initialization
### Arrays.asList()
- Arrays.asList() – Initialize arraylist from array
```
ArrayList<String> names = new ArrayList<String>( Arrays.asList("alex", "brian", "charles") );
```
- ArrayList constructor --- Create ArrayList and add objects

```
ArrayList<String> names = new ArrayList<>();

//1. Add elements one by one
names.add("alex");
names.add("brian");
names.add("charles");

System.out.println(names);

HashMap<String, Integer> details = new HashMap<>();

details.put("keanu", 23);
details.put("max", 24);
details.put("john", 53);

//2. Add elements from other collection
names.addAll(details.keySet());

System.out.println(names);   // [alex, brian, charles, max, john, keanu]
```

- Initialize arraylist of lists

```
List<List<Integer>> marks = new ArrayList<>();

marks.add( Arrays.asList(10, 20, 30) );
marks.add( Arrays.asList(40, 50, 60) );
marks.add( Arrays.asList(70, 80, 90) );

for (List<Integer> mark : marks) {
   System.out.println(mark);
}
```

- what is  `java.util.Arrays$ArrayList` ?
It is a nested class inside the Arrays class. It is a fixed size or immutable list backed by an array.

```
//  immutable list
List<String> list = Arrays.asList("1", "2", "3", "4", "5");
System.out.println(list.getClass()); // class java.util.Arrays$ArrayList
list.remove("3");   // throw exception : UnsupportedOperationException

//  mutable list
List<String> list = new ArrayList<>();
```

- Arrays.asList()传递的数组必须是对象数组，而不是基本类型。
Arrays.asList()是泛型方法，传入的对象必须是对象数组。

```
int[] myArray = {1, 2, 3};
List myList = Arrays.asList(myArray);
System.out.println(myList.size());//1
System.out.println(myList.get(0));//数组地址值
System.out.println(myList.get(1));//报错：ArrayIndexOutOfBoundsException
int[] array = (int[]) myList.get(0);
System.out.println(array[0]);//1
```

当传入一个原生数据类型数组时，Arrays.asList() 的真正得到的参数就不是数组中的元素，而是数组对象本身！

```
Integer[] myArray = {1, 2, 3};   // 使用包装类可解决问题
```

### Collections.swap
public static void swap(List list, int i, int j)

### Collection.toArray()
泛型方法：<T> T[] toArray(T[] a); 如果toArray方法中没有传递任何参数的话返回的是Object类型数组。

```
String [] s= new String[]{
    "dog", "lazy", "a", "over", "jumps", "fox", "brown", "quick", "A"
};
List<String> list = Arrays.asList(s);
Collections.reverse(list);
s=list.toArray(new String[0]);//没有指定类型的话会报错
```

[Details: Arrays of Wisdom of the Ancients](https://shipilev.net/blog/2016/arrays-wisdom-ancients/)

## Queue

```
Queue<String> queue = new LinkedList<String>();  // LinkedList类实现了Queue接口
```

![queue method](/assets/img/sample/queue.png)
The `offer` method inserts an element if possible, otherwise returning false. This differs from the `Collection.add` method, which can fail to add an element only by throwing an unchecked exception. The offer method is designed for use when failure is a normal, rather than exceptional occurrence, for example, in fixed-capacity (or "bounded") queues.
The `remove()` and `poll()` methods remove and return the head of the queue. Exactly which element is removed from the queue is a function of the queue's ordering policy, which differs from implementation to implementation. The `remove()` and `poll()` methods differ only in their behavior when the queue is empty: the `remove()` method throws an exception, while the `poll()` method returns `null`.
The `element()` and `peek()` methods return, but do not remove, the head of the queue.

## BigDecimal & BigInteger
- 浮点值判断
浮点数之间的等值判断，基本数据类型不能用==来比较，包装数据类型不能用 equals 来判断。
解决方法：使用使用 BigDecimal 来定义浮点数的值，再进行浮点数的运算操作。

```
float a = 1.0f - 0.9f;
float b = 0.9f - 0.8f;
System.out.println(a);// 0.100000024
System.out.println(b);// 0.099999964
System.out.println(a == b);// false

BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");

BigDecimal x = a.subtract(b);
BigDecimal y = b.subtract(c);

System.out.println(x); /* 0.1 */
System.out.println(y); /* 0.1 */
System.out.println(Objects.equals(x, y)); /* true */
```

- 保留小数

```
BigDecimal m = new BigDecimal("1.255433");
BigDecimal n = m.setScale(3,BigDecimal.ROUND_HALF_DOWN);
System.out.println(n);// 1.255
```

- 如何防止精度丢失
![精度](/assets/img/sample/bigdecimal.png)

## Map
### Map Initialization

```
// method 1
public static Map<String, String> articleMapOne;
static {
    articleMapOne = new HashMap<>();
    articleMapOne.put("ar01", "Intro to Map");
    articleMapOne.put("ar02", "Some article");
}

// method 2
public static Map<String, String> createSingletonMap() {
    return Collections.singletonMap("username1", "password1");
}

// empty map
Map<String, String> emptyMap = Collections.emptyMap();

// method 3
Map<String, String> map = Stream.of(new String[][] {
  { "Hello", "World" },
  { "John", "Doe" },
}).collect(Collectors.toMap(data -> data[0], data -> data[1]));

// method 4
Map<String, Integer> map = Stream.of(
  new AbstractMap.SimpleEntry<>("idea", 1),
  new AbstractMap.SimpleEntry<>("mobile", 2))
  .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
```

### TreeMap --- Comparator
![hierarchy](/assets/img/sample/treemap-hierarchy.jpg)
Key Points: [Source](https://www.callicoder.com/java-treemap/)
- A TreeMap is always sorted based on keys. You cannot sort TreeMap on values. The sorting order follows the natural ordering of keys. You may also provide a custom Comparator to the TreeMap at the time of creation to let it sort the keys using the supplied Comparator.
- A TreeMap cannot contain duplicate keys.
- TreeMap cannot contain the `null` key. However, It can have `null` values.
- TreeMap is not synchronized. Access to TreeMaps must be synchronized explicitly in a multi-threaded environment.

```java
import java.util.Comparator;
import java.util.SortedMap;
import java.util.TreeMap;

public class CreateTreeMapCustomComparatorExample {
    public static void main(String[] args) {
        // Creating a TreeMap with a Custom comparator (Descending order)
        SortedMap<String, String> fileExtensions = new TreeMap<>(new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return s2.compareTo(s1);
            }
        });

        /*
            The above TreeMap with custom Comparator can be simply written as -
            SortedMap<String, String> fileExtensions = new TreeMap<>(Comparator.reverseOrder());
        */

        // TreeMap with keys sorted by ignoring case
         SortedMap<String, String> fileExtensions2 = new TreeMap<>(String.CASE_INSENSITIVE_ORDER);

        // Adding new key-value pairs to a TreeMap
        fileExtensions.put("python", ".py");
        fileExtensions.put("c++", ".cpp");
        fileExtensions.put("kotlin", ".kt");
        fileExtensions.put("golang", ".go");
        fileExtensions.put("java", ".java");

        // Printing the TreeMap (The keys will be sorted based on the supplied comparator)
        System.out.println(fileExtensions);

    }
}
```

### Immutable Map

```
// for short map
ImmutableMap.of(key, value, key2, value2); // ...up to five k-v pairs
// for longer map
ImmutableMap<String,String> myMap = ImmutableMap.<String, String>builder()
    .put("key1", "value1")
    .put("key2", "value2")
    .put("key3", "value3")
    .build();
assertTrue(immutableMap.containsKey("key1"));
assertTrue(immutableMap.containsKey("key2"));
```

