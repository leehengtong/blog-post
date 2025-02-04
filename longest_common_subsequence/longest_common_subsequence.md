
最长公共子序列(Longest Common Subsequence, LCS)属于最基本也是最经典的动态规划问题，下面我们将以一道LeetCode上面的题目对这个问题进行解析和求解。

<!-- more -->

### [leetcode 1143: Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)

> Given two strings text1 and text2, return the length of their longest common subsequence.
> 
> A subsequence of a string is a new string generated from the original string with some characters(can be none) deleted without changing the relative order of the remaining characters. (eg, "ace" is a > subsequence of "abcde" while "aec" is not). A common subsequence of two strings is a subsequence that is common to both strings.
> 
> If there is no common subsequence, return 0.
> 
> Example 1:
> Input: text1 = "abcde", text2 = "ace" 
> Output: 3  
> Explanation: The longest common subsequence is "ace" and its length is 3.
> 
> Example 2:
> Input: text1 = "abc", text2 = "abc"
> Output: 3
> Explanation: The longest common subsequence is "abc" and its length is 3.
> 
> Example 3:
> Input: text1 = "abc", text2 = "def"
> Output: 0
> Explanation: There is no such common subsequence, so the result is 0.
>  
> 
> Constraints:
> 1 <= text1.length <= 1000
> 1 <= text2.length <= 1000
> 
> The input strings consist of lowercase English characters only.

这个题目比较好理解，我们首先建立一个二维的data数组，data数组中的每一个元素data[i][j]表示text1中的前i个字符和text2中的前j个字符的最长公共子序列的个数。那么对于data[i][j]我们有以下的递推关系：
- 如果text1[i-1]和text[j-1]相同，那么data[i][j] = data[i-1][j-1] + 1
- 如果text1[i-1]和text[j-1]不相同，那么data[i][j] = max(data[i-1][j], data[i][j-1])

具体的代码如下所示：

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        vector<vector<int>> data(text1.size() + 1, vector<int>(text2.size() + 1, 0));
        int res = 0;
        for (int i = 0; i < text1.size(); ++i) {
            for (int j = 0; j < text2.size(); ++j) {
                if (text1[i] == text2[j]) {
                    data[i+1][j+1] = data[i][j] + 1;
                } else {
                    data[i+1][j+1] = max(data[i][j+1], data[i+1][j]);
                }
                
                res = max(res, data[i+1][j+1]);
            }
        }
        
        return res;
    }
};
```
