---
title: Data Structure and Algorithms
date: 2021-02-24 22:10:22 +0800
categories: [datastructure, notes]
tags: [notes]
---
[Tutorials](https://www.cs.auckland.ac.nz/software/AlgAnim/Index.html)

# Sort Algorithms
[](https://juejin.cn/post/6844903685013651470)
[Visualization](http://www.algostructure.com/index.php)

## Quick Sort
```java
// 方法一：
public class QuickSort {

    public static void main(String[] args) {
        int array[]={32, 12, 7, 78, 23, 45};
        quickSort(array,0,array.length-1);
        System.out.println(Arrays.toString(array));
    }
    public static void quickSort(int array[],int left,int right)
    {
        if(left>=right)
        {
            return ;
        }
        int i=left;
        int j=right;
        int key=array[left];
        while(i<j)
        {
            while(i<j&&array[j]>key)
            {
                j--;
            }
            array[i]=array[j];
            //从后往前找到第一个比key小的数与array[i]交换；
            while(i<j&&array[i]<key)
            {
                i++;
            }
            array[j]=array[i];
            //从前往后找到第一个比key大的数与array[j]交换；
        }
        array[i]=key;
        //一趟快排之后已经将key的位置找到。
        quickSort(array,left,i-1);
        //对key左边的进行排序
        quickSort(array,i+1,right);
        //对key右边的进行排序
    }
    // 方法二：
     public void quickSort(int[] arr, int low, int high) {
        // low,high 为每次处理数组时的首、尾元素索引

        //当low==high是表示该序列只有一个元素，不必排序了
        if (low >= high) {
            return;
        }
        // 选出哨兵元素和基准元素。这里左边的哨兵元素也是基准元素
        int i = low, j = high, base = arr[low];
        while (i < j) {
            //右边哨兵从后向前找
            while (arr[j] >= base && i < j) {
                j--;
            }
            //左边哨兵从前向后找
            while (arr[i] <= base && i < j) {
                i++;
            }
            swap(arr,i,j);  //交换元素
        }
        swap(arr,low,j);  //基准元素与右哨兵交换

        //递归调用，排序左子集合和右子集合
        quickSort(arr,low,j-1);
        quickSort(arr,j+1,high);
    }

    private void swap(int[] arr, int i, int j) {
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
}


```

## Shell Sort
![](/assets/img/sample/shell-sort.png)
```
void shellSort(int a[], int n) {
   for (int gap = n / 2; gap > 0; gap /= 2) {
       for (int i = gap; i < n; i++) {
           int key = a[i];
           int j;
           for (j = i; j >= gap && key < a[j-gap]; j -= gap) {
               a[j] = a[j-gap];
           }
           a[j] = key;
       }
   }
}
```

## CountingSort
```java
class CountingSort {
    void maxValue(int a[]) {
       int max = Integer.MIN_VALUE;
       for (int num : a) {
         if (num > max)
           max = num;
       }
       return max;
    }
    void sort(int a[]) {
       int n = a.length;
       int k = maxValue(a);
       int[] count = new int[k];
       int[] output = new int[n];

       for (int i = 0; i < n; i++) {
          ++count[a[i]];
       }

       for (int i = 1; i < k; i++) {
          count[i] += count[i-1];
       }

       for (int j = n-1; j >= 0; j--) {  // 倒序，保证稳定性
          output[count[a[j]] - 1] = a[j];
          --count[a[j]];
       }

       for (int i = 0; i < n; i++) {
          a[i] = output[i];
       }
    }
}
```
Time Complexity: O(n+k) where n is the number of elements in input array and k is the range of input.
Auxiliary Space: O(n+k)

## MergeSort
Time Complexity: O(NlgN）
```java
class mergeSortAlgorithm {
    void mergeSort (int[] a, int l, int r) {
        if (l < r) {
            int m = l + (r - l) / 2;
            mergeSort(a, l, m);
            mergeSort(a, m + 1, r);
            merge(a, l, m, r);
        }
    }
    void merge (int[] a, int l, int m, int r) {
        int n1 = m - l + 1;
        int n2 = r - m;
        int[] left = new int[n1];
        int[] right = new int[n2];

        int i = 0; int j = 0;
        int k = l;
        for (; i < n1; i++) {
           left[i] = a[l + i];
        }
        for (; j < n2; j++) {
           right[j] = a[m + 1 + j];
        }

        i = l; j = m + 1;
        while (i < n1 && j < n2) {
           if (left[i] <= right[j]) {
              a[k++] = left[i++];
           }
           else {
              a[k++] = right[j++];
           }
        }
        while (i < n1) {
           a[k++] = left[i];
        }
        while (j < n2) {
           a[k++] = right[j];
        }
    }
    public static void main(String[] args){
       int arr[] = { 12, 11, 13, 5, 6, 7 };
       mergeSortAlgorithm sortAlgorithm = new mergeSortAlgorithm();
       sortAlgorithm.mergeSort(arr, 0, arr.length - 1);
    }
}

```

## Radix Sort
[radix sort](https://www.geeksforgeeks.org/radix-sort/)

```java
// Radix sort Java implementation
import java.io.*;
import java.util.*;

class Radix {

	// A utility function to get maximum value in arr[]
	static int getMax(int arr[], int n)
	{
		int mx = arr[0];
		for (int i = 1; i < n; i++)
			if (arr[i] > mx)
				mx = arr[i];
		return mx;
	}

	// A function to do counting sort of arr[] according to
	// the digit represented by exp.
	static void countSort(int arr[], int n, int exp)
	{
		int output[] = new int[n]; // output array
		int i;
		int count[] = new int[10];
		Arrays.fill(count, 0);

		// Store count of occurrences in count[]
		for (i = 0; i < n; i++)
			count[(arr[i] / exp) % 10]++;

		// Change count[i] so that count[i] now contains
		// actual position of this digit in output[]
		for (i = 1; i < 10; i++)
			count[i] += count[i - 1];

		// Build the output array
		for (i = n - 1; i >= 0; i--) {
			output[count[(arr[i] / exp) % 10] - 1] = arr[i];
			count[(arr[i] / exp) % 10]--;
		}

		// Copy the output array to arr[], so that arr[] now
		// contains sorted numbers according to curent digit
		for (i = 0; i < n; i++)
			arr[i] = output[i];
	}

	// The main function to that sorts arr[] of size n using
	// Radix Sort
	static void radixsort(int arr[], int n)
	{
		// Find the maximum number to know number of digits
		int m = getMax(arr, n);

		// Do counting sort for every digit. Note that
		// instead of passing digit number, exp is passed.
		// exp is 10^i where i is current digit number
		for (int exp = 1; m / exp > 0; exp *= 10)
			countSort(arr, n, exp);
	}

	// A utility function to print an array
	static void print(int arr[], int n)
	{
		for (int i = 0; i < n; i++)
			System.out.print(arr[i] + " ");
	}

	/*Driver Code*/
	public static void main(String[] args)
	{
		int arr[] = { 170, 45, 75, 90, 802, 24, 2, 66 };
		int n = arr.length;

		// Function Call
		radixsort(arr, n);
		print(arr, n);
	}
}
/* This code is contributed by Devesh Agrawal */

```
# Building Heap from Array
[Detail](https://www.geeksforgeeks.org/building-heap-from-array/)
- **Time Complexity**: Heapify a single node takes O(log N) time complexity where N is the total number of Nodes. Therefore, building the entire Heap will take N heapify operations and the total time complexity will be **O(N*logN)**.
```java
// Java program for building Heap from Array

public class BuildHeap {

	// To heapify a subtree rooted with node i which is
	// an index in arr[].Nn is size of heap
	static void heapify(int arr[], int n, int i)
	{
		int largest = i; // Initialize largest as root
		int l = 2 * i + 1; // left = 2*i + 1
		int r = 2 * i + 2; // right = 2*i + 2

		// If left child is larger than root
		if (l < n && arr[l] > arr[largest])
			largest = l;

		// If right child is larger than largest so far
		if (r < n && arr[r] > arr[largest])
			largest = r;

		// If largest is not root
		if (largest != i) {
			int swap = arr[i];
			arr[i] = arr[largest];
			arr[largest] = swap;

			// Recursively heapify the affected sub-tree
			heapify(arr, n, largest);
		}
	}

	// Function to build a Max-Heap from the Array
	static void buildHeap(int arr[], int n)
	{
		// Index of last non-leaf node
		int startIdx = (n / 2) - 1;

		// Perform reverse level order traversal
		// from last non-leaf node and heapify
		// each node
		for (int i = startIdx; i >= 0; i--) {
			heapify(arr, n, i);
		}
	}

	// A utility function to print the array
	// representation of Heap
	static void printHeap(int arr[], int n)
	{
		System.out.println("Array representation of Heap is:");

		for (int i = 0; i < n; ++i)
			System.out.print(arr[i] + " ");

		System.out.println();
	}

	// Driver Code
	public static void main(String args[])
	{
		// Binary Tree Representation
		// of input array
		//       1
		//		 /	 \
		//    3		  5
		//	 / \	 / \
		//  4	  6 13 10
		// / \ / \
		// 9 8 15 17
		int arr[] = { 1, 3, 5, 4, 6, 13, 10,
					9, 8, 15, 17 };

		int n = arr.length;

		buildHeap(arr, n);
		printHeap(arr, n);
	}
}

```

# Tree
[CS241 Lecture Notes](https://www.cpp.edu/~ftang/courses/CS241/notes/b-tree.htm)
## AVL Tree
AVL tree is a self-balancing Binary Search Tree (BST).where the difference between heights of left and right subtrees cannot be more than one for all nodes.
Why AVL Trees?
Most of the BST operations (e.g., search, max, min, insert, delete.. etc) take O(h) time where h is the height of the BST. The cost of these operations may become O(n) for a skewed Binary tree. If we make sure that height of the tree remains O(Logn) after every insertion and deletion, then we can guarantee an upper bound of O(Logn) for all these operations.

## B-Tree
![b-tree-structure](/assets/img/sample/b-tree-structure.jpg)
[](https://www.geeksforgeeks.org/introduction-of-b-tree-2/)
![](/assets/img/sample/btree.png)
- a self-balancing search tree
- The main idea of using B-Trees is to reduce the number of disk accesses.
- Time Complexity: Search, Insert, Delete : O(log n)
- Generally, the B-Tree node size is kept equal to the disk block size.

### Properties of B-Tree:
1. All leaves are at the same level.
2. A B-Tree is defined by the term minimum degree ‘t’. The value of t depends upon disk block size.
3. Every node except root must contain at least (ceiling)(\[t-1]/2) keys. The root may contain minimum 1 key.
4. All nodes (including root) may contain at most t – 1 keys.
5. Number of children of a node is equal to the number of keys in it plus 1.
6. All keys of a node are sorted in increasing order. The child between two keys k1 and k2 contains all keys in the range from k1 and k2.
7. B-Tree grows and shrinks from the root which is unlike Binary Search Tree. Binary Search Trees grow downward and also shrink from downward.
8. Like other balanced Binary Search Trees, time complexity to search, insert and delete is O(log n).

## B+ Tree
[b-plus-tree-structure](/assets/img/sample/b-plus-tree-structure.jpg)
[B+ TREE : Search, Insert and Delete Operations Example](https://www.guru99.com/introduction-b-plus-tree.html)
![](/assets/img/sample/b-plus-tree.png)
The order, or branching factor, b of a B+ tree measures the capacity of nodes (i.e., the number of children nodes) for internal nodes in the tree. The actual number of children for a node, referred to here as m, is constrained for internal nodes so that ![](../assets/img/sample/b-tree-order.png).
- The advantages of B+ Tree
Because B+ trees don’t have data associated with interior nodes, more keys can fit on a page of memory. Therefore, it will require fewer cache misses in order to access data that is on a leaf node.

The leaf nodes of B+ trees are linked, so doing a full scan of all objects in a tree requires just one linear pass through all the leaf nodes. A B tree, on the other hand, would require a traversal of every level in the tree. This full-tree traversal will likely involve more cache misses than the linear traversal of B+ leaves.

### Applications
- database indexing

## Red-Black Trees
[Red-Black Trees](https://www.cs.auckland.ac.nz/software/AlgAnim/red_black.html)
### Properties
A red-black tree is a binary search tree which has the following red-black properties:
- Every node is either red or black.
- Every leaf (NULL) is black.
- If a node is red, then both its children are black.
- Every simple path from a node to a descendant leaf contains the same number of black nodes.

### Applications
- The process scheduler in Linux uses Red Black Trees. The Completely Fair Scheduler (CFS) is the name of a process scheduler which was merged into the 2.6.23 release of the Linux kernel. It handles CPU resource allocation for executing processes, and aims to maximize overall CPU utilization while also maximizing interactive performance. The nodes are indexed by processor "execution time" in nanoseconds.

## BitMap
**优点：**
- 占用内存少
- 运算效率高
**缺点：**
- 数据碰撞。比如将字符串映射到 BitMap 的时候会有碰撞的问题，那就可以考虑用 Bloom Filter 来解决，Bloom Filter 使用多个 Hash 函数来减少冲突的概率。
- 数据稀疏。又比如要存入(10,8887983,93452134)这三个数据，我们需要建立一个 99999999 长度的 BitMap ，但是实际上只存了3个数据，这时候就有很大的空间浪费，碰到这种问题的话，可以通过引入 Roaring BitMap 来解决。
```java
public class BitMap {
    //保存数据的
    private int[] bits;   // 1int = 4 bytes = 32 bits

    //能够存储多少数据
    private int capacity;


    public BitMap(int capacity){
        this.capacity = capacity;

        //1int能存储2 ^ 5 = 32个数据，那么capacity数据需要多少个bit呢，capacity/32+1
        bits = new int[(capacity >> 5) + 1];
    }

    public void add(int num){
        // num/32得到int[]的index
        int arrayIndex = num >> 5;

        // num%32得到在int[index]的位置
        int position = num & ((1 << 5) - 1);

        //将1左移position后，那个位置自然就是1，然后和以前的数据做|，这样，那个位置就替换成1了。
        bits[arrayIndex] |= 1 << position;
    }

    public boolean contain(int num){
        // num/32得到int[]的index
        int arrayIndex = num >> 3;

        // num%32得到在int[index]的位置
        int position = num & ((1 << 5) - 1);

        //将1左移position后，那个位置自然就是1，然后和以前的数据做&，判断是否为0即可
        return (bits[arrayIndex] & (1 << position)) !=0;
    }

    public void clear(int num){
        // num/32得到int[]的index
        int arrayIndex = num >> 3;

        // num%32得到在int[index]的位置
        int position = num & ((1 << 5) - 1);

        //将1左移position后，那个位置自然就是1，然后对取反，再与当前值做&，即可清除当前的位置了.
        bits[arrayIndex] &= ~(1 << position);

    }

    public static void main(String[] args) {
        BitMap bitmap = new BitMap(100);
        bitmap.add(7);
        System.out.println("插入7成功");

        boolean isexsit = bitmap.contain(7);
        System.out.println("7是否存在:"+isexsit);

        bitmap.clear(7);
        isexsit = bitmap.contain(7);
        System.out.println("7是否存在:"+isexsit);
    }
}
```
**应用：**
- 判断是否存在重复整数。
- 在2.5亿个整数中找出不重复的整数。注：内存不足以容纳这2.5亿个整数。
- 已知某个文件内包含一些电话号码，每个号码为8位数字，统计不同号码的个数。
### Bloom Filter
Bloom Filter 是由一个长度为 m 的比特位数组（bit array）与 k 个哈希函数（hash function）组成的数据结构。位数组均初始化为 0，所有哈希函数都可以分别把输入数据尽量均匀地散列。

当要插入一个元素时，将其数据分别输入 k 个哈希函数，产生 k 个哈希值。以哈希值作为位数组中的下标，将所有 k 个对应的比特置为 1。

当要查询（即判断是否存在）一个元素时，同样将其数据输入哈希函数，然后检查对应的 k 个比特。如果有任意一个比特为 0，表明该元素一定不在集合中。如果所有比特均为 1，表明该元素有（较大的）可能性在集合中。为什么不是一定在集合中呢？因为一个比特被置为 1 有可能会受到其他元素的影响，这就是所谓“假阳性”（false positive）。相对地，“假阴性”（false negative）在 Bloom Filter 中是绝不会出现的。
![](/assets/img/sample/Bloom-Filter-Summary-02.png)
**优点：**
不需要存储数据本身，只用比特表示，因此空间占用相对于传统方式有巨大的优势，并且能够保密数据；
时间效率也较高，插入和查询的时间复杂度均为O(k)；
哈希函数之间相互独立，可以在硬件指令层面并行计算。
**缺点：**
存在假阳性的概率，不适用于任何要求 100% 准确率的场景；
只能插入和查询元素，不能删除元素，这与产生假阳性的原因是相同的。我们可以简单地想到通过计数（即将一个比特扩展为计数值）来记录元素数，但仍然无法保证删除的元素一定在集合中。
### Roaring Bitmap
[RoaringBitmap数据结构及原理](https://blog.csdn.net/yizishou/article/details/78342499)
[Apache Kylin 原理介绍与新架构分享（Kylin On Parquet）](https://www.infoq.cn/article/vOrjsJCgVAVPim5hsj6p)
![roaringbitmap-introduction](/assets/img/sample/roaringbitmap-01.png)
![array-container](/assets/img/sample/roaringbitmap-02.png)
![bitmap-container](/assets/img/sample/roaringbitmap-03.png)
