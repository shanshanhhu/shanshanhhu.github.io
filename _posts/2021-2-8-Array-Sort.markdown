---
title: Arrary Sort
date: 2021-2-8 12:08:22 +0800
categories: [algorithm, sort]
tags: [sort]
---

# Merge Sort
[Source](https://www.geeksforgeeks.org/merge-sort/)
![merge sort](../assets/img/sample/Merge-Sort-Tutorial.png)
## Array Merge Sort Code
```cplusplus
// C++ program for Merge Sort
#include <iostream>
using namespace std;

// Merges two subarrays of arr[].
// First subarray is arr[l..m]
// Second subarray is arr[m+1..r]
void merge(int arr[], int l, int m, int r)
{
	int n1 = m - l + 1;
	int n2 = r - m;

	// Create temp arrays
	int L[n1], R[n2];

	// Copy data to temp arrays L[] and R[]
	for (int i = 0; i < n1; i++)
		L[i] = arr[l + i];
	for (int j = 0; j < n2; j++)
		R[j] = arr[m + 1 + j];

	// Merge the temp arrays back into arr[l..r]

	// Initial index of first subarray
	int i = 0;

	// Initial index of second subarray
	int j = 0;

	// Initial index of merged subarray
	int k = l;

	while (i < n1 && j < n2) {
		if (L[i] <= R[j]) {
			arr[k] = L[i];
			i++;
		}
		else {
			arr[k] = R[j];
			j++;
		}
		k++;
	}

	// Copy the remaining elements of
	// L[], if there are any
	while (i < n1) {
		arr[k] = L[i];
		i++;
		k++;
	}

	// Copy the remaining elements of
	// R[], if there are any
	while (j < n2) {
		arr[k] = R[j];
		j++;
		k++;
	}
}

// l is for left index and r is
// right index of the sub-array
// of arr to be sorted */
void mergeSort(int arr[],int l,int r){
	if(l>=r){
		return;//returns recursively
	}
	int m =l+ (r-l)/2;
	mergeSort(arr,l,m);
	mergeSort(arr,m+1,r);
	merge(arr,l,m,r);
}

// UTILITY FUNCTIONS
// Function to print an array
void printArray(int A[], int size)
{
	for (int i = 0; i < size; i++)
		cout << A[i] << " ";
}

// Driver code
int main()
{
	int arr[] = { 12, 11, 13, 5, 6, 7 };
	int arr_size = sizeof(arr) / sizeof(arr[0]);

	cout << "Given array is \n";
	printArray(arr, arr_size);

	mergeSort(arr, 0, arr_size - 1);

	cout << "\nSorted array is \n";
	printArray(arr, arr_size);
	return 0;
}
```
## Performance Analysis
- Time Complexity:
T(n) = 2T(n/2) + θ(n) --->  θ(nLogn)
- Auxiliary Space: O(n)
- Stable : Yes

## Applications
- **sorting linked list**
[Source](https://www.geeksforgeeks.org/merge-sort-for-linked-list/)
  **Leetcode Problem** :
148/. Sort List
Given the head of a linked list, return the list after sorting it in ascending order.
Follow up: Can you sort the linked list in O(n logn) time and O(1) memory (i.e. constant space)?
```cplusplus
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        mergeSort(&head);
        return head;
    }

    void splitList(ListNode* head, ListNode** a, ListNode** b) {
        ListNode* fast = head->next;    // !!!!
        ListNode* slow = head;
        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
        }
        *a = head;
        *b = slow->next;
        slow->next = nullptr;
    }

    ListNode* merge(ListNode* a, ListNode* b) {
        ListNode* res = nullptr;
        if (!a) return b;      // !!!!
        else if (!b)
            return a;

        if (a->val <= b->val) {
            res = a;
            res->next = merge(a->next, b);
        }
        else {
            res = b;
            res->next = merge(a, b->next);
        }

        return res;
    }

    void mergeSort(ListNode** headRef) {
        ListNode* head = *headRef;
        ListNode* a;
        ListNode* b;
        if (!head || !head->next) return;

        splitList(head, &a, &b);
        mergeSort(&a);
        mergeSort(&b);

        *headRef = merge(a, b);
    }
};
```


- **Count Inversions in an array**
[Source](https://www.geeksforgeeks.org/counting-inversions/)

