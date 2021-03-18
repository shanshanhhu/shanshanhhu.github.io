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
## Shell Sort
![](../assets/img/sample/shell-sort.png)
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
[](https://www.geeksforgeeks.org/introduction-of-b-tree-2/)
![](../assets/img/sample/btree.png)
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
[B+ TREE : Search, Insert and Delete Operations Example](https://www.guru99.com/introduction-b-plus-tree.html)
![](../assets/img/sample/b-plus-tree.png)
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
