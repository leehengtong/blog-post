
最长公共递增子序列(Longest Common Increasing Subsequence, LCIS)是[LCS](https://leehengtong.com/2021/02/06/longest_common_subsequence/)和[LIS](https://leehengtong.com/2021/02/14/longest_increasing_subsequence/)的结合体，比这两个基本形式都复杂一些。下面我们将以一道GeeksForGeeks上面的题目对这个问题进行解析和求解。

<!-- more -->

### [GeeksForGeeks: Longest Common Increasing Subsequence (LCS + LIS)](https://www.geeksforgeeks.org/longest-common-increasing-subsequence-lcs-lis/)
> Given two arrays, find length of the longest common increasing subsequence [LCIS] and print one of such sequences (multiple sequences may exist)
> 
> Suppose we consider two arrays:
> arr1[] = {3, 4, 9, 1} and
> arr2[] = {5, 3, 8, 9, 10, 2, 1}
> 
> Our answer would be {3, 9} as this is the longest common subsequence which is increasing also.

对于这个题目，我们仍然使用动态规划来求解，只不过我们使用一维数组来隐含的表示二维状态。我们先给出代码：
```c++
class Solution{   
public:
    int LCIS(int arr1[], int m, int arr2[], int n) {
        vector<int> data(n, 0);
        
        for (int i = 0; i < m; ++i) {
            int cur = 0;  // The current max LCIS length without using arr1[i] 
            for (int j = 0; j < n; ++j) {            
                if (arr1[i] == arr2[j]) {  // We can construct a longer LCIS end with arr2[j] using arr1[i] 
                    if (cur + 1 > data[j]) {
                        data[j] = cur + 1;
                    }
                }
                if (arr1[i] > arr2[j]) {  // We found a longer LCIS to which the arr1[i] can append
                    if (data[j] > cur) {
                        cur = data[j];
                    }
                }
            }
        }
        
        return *std::max_element(data.begin(), data.end());
    }
};
```
其中，在第二个for循环里面，(1) data[j]表示由arr1[0...i]和arr2[0...j]所形成的最长LCIS的长度; (2) cur表示由arr1[0...i-1]和arr2[0...j]所形成的并且arr1[i]可以加在其后的最长的LCIS的长度。
- 当当前的arr1[i]和arr2[j]相等时，如果新形成的LCIS的长度大于已有的以arr2[j]结尾的LCIS，更新data[j];
- 如果以arr2[j]结尾的LCIS的长度大于cur，并且arr1[i]可以放在这个LCIS的尾端的时候，我们认为在后续循环中以这个LCIS为基础来构造更长的LCIS,所以更新cur的值；
