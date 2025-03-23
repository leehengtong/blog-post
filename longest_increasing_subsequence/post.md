最长递增子序列(Longest Increasing Subsequence, LIS)属于最基本也是最经典的动态规划问题，下面我们将以一道LeetCode上面的题目对这个问题进行解析和求解。

<!-- more -->

### [leetcode 300: Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

> Given an integer array nums, return the length of the longest strictly increasing subsequence.
> 
> A subsequence is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements. For example, [3,6,2,7] is a subsequence of the array [0,3,1,6,2,2,7].
> 
> 
> Example 1:
> Input: nums = [10,9,2,5,3,7,101,18]
> Output: 4
> Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
> 
> Example 2:
> Input: nums = [0,1,0,3,2,3]
> Output: 4
> 
> Example 3:
> Input: nums = [7,7,7,7,7,7,7]
> Output: 1
>  
> 
> Constraints:
> 1 <= nums.length <= 2500
> -104 <= nums[i] <= 104
>  
> 
> Follow up:
> - Could you come up with the $O(n^2)$ solution?
> - Could you improve it to $O(n log(n))$ time complexity?

一种符合直觉的算法是$O(n^2)$的算法，这也是会被大多数ACMer所鄙视的一种算法。我们可以使用一个数组data来表示以每个数字结尾的最长LIS的大小，即data[i]表示以nums[i]结尾的最长的LIS的长度。在计算nums[i]对应的data[i]的时候，我们只需要遍历每一个在i前面的数字nums[j],如果nums[j] < nums[i], 那么就把nums[i]可以放在nums[j]的任意LIS后面，并把这个LIS的长度加1，从而可以以此更新nums[i]对应的data[i]. 具体代码如下所示：
```C++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> data(nums.size(), 1);
        int res = nums.empty() ? 0 : 1;
        for (int i = 1; i < nums.size(); ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[j] < nums[i]) {
                    data[i] = max(data[i], data[j] + 1);
                }
            }
            res = max(res, data[i]);
        }
        
        return res;
    }
};
```

为了获得一个$O(n log(n))$的算法，我们可以对上面的算法进行优化，即对7-11行的查找过程进行优化，实现$O(logn)$的查找。不过，下面我们给出另外一个不同思路的算法。在这个算法中，我们不是直接去构造LIS，而是维护一个数据结构data，data[i]表示对于长度为$i+1$的LIS，其结尾的数字最小可以是多少。让我们先看一下算法实现：
```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> data;
        for (int i : nums) {
            auto pos = lower_bound(data.begin(), data.end(), i);
            if (pos == data.end()) {
                data.push_back(i);
            } else {
                if (*pos > i) {
                    *pos = i;
                }
            }
        }
        
        return data.size();
    }
};
```
请注意最终的data并不是一个LIS，但是data的长度却表示可以构造的最长的LIS。下面我们以[10,9,2,5,3,7,101,18]为例进行推演一下：
1. i = 10, data = [], pos = 0 -> data = [10]
2. i = 9, data = [10], pos = 0 -> data = [9]
3. i = 2, data = [9], pos = 0 -> data = [2]
4. i = 5, data = [2], pos = 1 -> data = [2, 5]
5. i = 3, data = [2, 5], pos = 1 -> data = [2, 3]
6. i = 7, data = [2, 3], pos = 2 -> data = [2, 3, 7]
7. i = 101, data = [2, 3, 7], pos = 3 -> data = [2, 3, 7, 101]
8. i = 18, data = [2, 3, 7, 101], pos = 3 -> data = [2, 3, 7, 18]

两种典型的更新体现在第5步和第7步：第5步的更新使得LIS的结尾数字更小；第7步的更新加长LIS
