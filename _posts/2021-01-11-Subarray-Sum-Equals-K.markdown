---
title: Subarray Sum Equals K
date: 2021-01-11 09:37:22 +0800
categories: [leetcode, dynamicprograming]
tags: [dynamicprograming]     # TAG names should always be lowercase
---

# 560. Subarray Sum Equals K
**[Problem:](https://leetcode.com/problems/subarray-sum-equals-k/)**
    Given an array of integers nums and an integer k, return the total number of continuous subarrays whose sum equals to k.

**Example:**
> Input: nums = \[1,1,1], k = 2
> Output: 2
```
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n = nums.size();

        unordered_map<int, int> sum;
        sum[0] = 1;
        int res = 0; int cursum = 0;
        for (int i = 0; i < n; i++) {
            cursum += nums[i];
            if (sum.find(cursum - k) != sum.end()) {
                res += sum[cursum - k];
            }
            sum[cursum]++;
        }
        return res;
    }
};
```

# 209. Minimum Size Subarray Sum
**[Problem:](https://leetcode.com/problems/minimum-size-subarray-sum/)**
Given an array of n positive integers and a positive integer s, find the minimal length of a contiguous subarray of which the sum ≥ s. If there isn't one, return 0 instead.

**Example:**
> Input: s = 7, nums = \[2,3,1,2,4,3]
> Output: 2
> Explanation: the subarray \[4,3] has the minimal length under the problem constraint.
```
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int res = INT_MAX; int cursum = 0;
        for(int i = 0; i < nums.size(); i++) {
            cursum += nums[i];
            nums[i] = cursum;
            if (cursum < s) continue;

            // Binary Search
            int l = 0; int r = i+1;
            while (l < r) {
                int mid = l + (r - l) / 2;
                if (nums[mid] > cursum - s) r = mid;
                else
                    l = mid + 1;
            }
            res = min(res, i - l + 1);
        }
        if (res == INT_MAX)
            res = 0;
        return res;
    }
};
```

# 862. Shortest Subarray with Sum at Least K
**[Problem:](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)**
Return the length of the shortest, non-empty, contiguous subarray of A with sum at least K.
If there is no non-empty subarray with sum at least K, return -1.

**Example:**
> Input: A = \[2,-1,2], K = 3
> Output: 3
```
class Solution {
public:
    int shortestSubarray(vector<int>& A, int K) {
        int n = A.size();

        deque<int> q;
        int res = INT_MAX;
        for (int i = 0; i < n; i++) {
            if (i > 0 )
                A[i] += A[i-1];

            if (A[i] >= K)
                res = min(res, i+1);

            while (q.size() && A[i] - A[q.front()] >= K) {
                res = min(res, i - q.front());
                q.pop_front();
            }

            while (q.size() && A[q.back()] >= A[i]) {
                q.pop_back();
            }
            q.push_back(i);
        }
        if (res == INT_MAX)
            return -1;
        return res;
    }
};
```

# 363. Max Sum of Rectangle No Larger Than K
**[Problem:](https://leetcode.com/problems/max-sum-of-rectangle-no-larger-than-k/)**
Given a non-empty 2D matrix matrix and an integer k, find the max sum of a rectangle in the matrix such that its sum is no larger than k.

**Example:**
> Input: matrix = \[\[1,0,1],\[0,-2,3]], k = 2
> Output: 2
> Explanation: Because the sum of rectangle \[\[0, 1], \[-2, 3]] is 2,
             and 2 is the max number no larger than k (k = 2).
## Largest Sum Contiguous Subarray
**[Kadane’s Algorithm:](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)**
```
Initialize:
    max_so_far = 0
    max_ending_here = 0

Loop for each element of the array
  (a) max_ending_here = max_ending_here + a[i]
  (b) if(max_so_far < max_ending_here)
            max_so_far = max_ending_here
  (c) if(max_ending_here < 0)
            max_ending_here = 0
return max_so_far
```
**[Solution:](https://leetcode.com/problems/max-sum-of-rectangle-no-larger-than-k/discuss/851448/C%2B%2B-from-800-ms-to-50-ms)**
```
class Solution {
public:
    int maxSumSubmatrix(vector<vector<int>>& matrix, int k) {
        int rows = matrix.size();
        int cols = matrix[0].size();
        int res = INT_MIN;

        for (int l = 0; l < cols; l++) {
            vector<int> sums(rows);
            for (int j = l; j < cols; j++) {
                int kadane = 0; int max_kadane = INT_MIN;
                for (int i = 0; i < rows; i++) {
                    sums[i] += matrix[i][j];
                    // kadane 算法优化
                    kadane = max(kadane + sums[i], sums[i]);
                    max_kadane = max(max_kadane, kadane);
                }
                if(max_kadane <= k) {
                    res = max(res, max_kadane);
                    continue;
                }
                set<int> s{0};
                int cursum = 0;
                for (int sum : sums) {
                    cursum += sum;
                    auto it = s.lower_bound(cursum - k);  // cursum <= k 的都符合要求
                    if (it != s.end()) {
                        res = max(res, cursum - *it);
                    }
                    s.insert(cursum);
                }
            }
        }
        return res;
    }
};
```

# 1074. Number of Submatrices That Sum to Target
[Problem:](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/)
Given a matrix and a target, return the number of non-empty submatrices that sum to target.
A submatrix x1, y1, x2, y2 is the set of all cells matrix\[x]\[y] with x1 <= x <= x2 and y1 <= y <= y2.
Two submatrices (x1, y1, x2, y2) and (x1', y1', x2', y2') are different
if they have some coordinate that is different: for example, if x1 != x1'.

**Example:**

![matrix photo](/assets/img/sample/leetcode1074.jpg)
> Input: matrix = \[\[0,1,0],\[1,1,1],\[0,1,0]], target = 0
>
> Output: 4
>
> Explanation: The four 1x1 submatrices that only contain 0.

```
class Solution {
public:
    int numSubmatrixSumTarget(vector<vector<int>>& matrix, int target) {
        int rows = matrix.size(), cols = matrix[0].size();
        int ans = 0;
        for (int l = 0; l < cols; l++) {
            vector<int> sum(rows);
            for (int c = l; c < cols; c++) {
                for (int r = 0; r < rows; r++) {
                    sum[r] += matrix[r][c];
                }
                unordered_map<int, int> map;
                map[0] = 1;
                int cursum = 0;
                for (auto s : sum) {
                    cursum += s;
                    if (map.find(cursum - target) != map.end()) {
                        ans += map[cursum - target];
                    }
                    map[cursum]++;
                }

            }
        }
        return ans;
    }
};
```
# 221. Maximal Square
[Problem:](https://leetcode.com/problems/maximal-square/)
Given an m x n binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.\
**Example:**\
![matrix photo](/assets/img/sample/leetcode221.jpg)\
Input: matrix = \[\["1","0","1","0","0"],\["1","0","1","1","1"],\["1","1","1","1","1"],\["1","0","0","1","0"]]\
Output: 4
```
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int rows = matrix.size();
        int cols = matrix[0].size();
        int res = 0;

        for (int l = 0; l < cols; l++) {
            int sums[rows];
            memset(sums, 0, sizeof(sums));
            for (int j = l; j < cols; j++) {
                for (int i = 0; i < rows; i++) {
                    sums[i] += matrix[i][j] - '0';
                }

                vector<int> cursum;
                cursum.push_back(0);
                for (int k = 0; k < rows; k++) {
                    cursum.push_back(cursum[k] + sums[k]);
                    if (k < j - l) continue;
                    int idx = min(k, j - l);
                    int area = pow(idx + 1, 2);
                    int tmp = cursum[k+1] - cursum[k - idx];
                    if (tmp == area)
                        res = max(res, area);
                }
            }
        }
        return res;
    }
};
```

# 85. Maximal Rectangle
[Problem:](https://leetcode.com/problems/maximal-rectangle/)
Given a rows x cols binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.
**Example:**

![matrix photo](/assets/img/sample/leetcode85.jpg)
> Input: matrix = \[\["1","0","1","0","0"],\["1","0","1","1","1"],\["1","1","1","1","1"],\["1","0","0","1","0"]]
>
> Output: 6
>
> Explanation: The maximal rectangle is shown in the above picture.

**[Solution:](https://leetcode.com/problems/maximal-rectangle/discuss/979263/C%2B%2B-DP-solution-99.07-faster-using-the-idea-of-Largest-Rect-in-Histogram)**
```
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if (matrix.size() == 0 || matrix[0].size() == 0) return 0;
        int r = matrix.size();
        int c = matrix[0].size();

        int left[c], right[c], height[c];
        fill_n(left, c, 0);
        fill_n(right, c, c);
        fill_n(height, c, 0);

        int ans = 0;
        for (int i = 0; i < r; i++) {
            int curleft = 0; int curright = c; // 每一行都要重新赋值
            for (int j = 0; j < c; j++) {
                if (matrix[i][j] == '1')
                    height[j]++;
                else
                    height[j] = 0;
            }

            for (int j = 0; j < c; j++) {
                if (matrix[i][j] == '1')
                    left[j] = max(left[j], curleft);
                else {
                    left[j] = 0;
                    curleft = j+1;
                }
            }

            for (int j = c-1; j >= 0; j--) {
                if (matrix[i][j] == '1')
                    right[j] = min(right[j], curright);
                else {
                    right[j] = c;
                    curright = j;
                }
            }

            for (int j = 0; j < c; j++) {
                ans = max(ans, (right[j]-left[j])*height[j]);
            }
        }
        return ans;
    }
};
```

