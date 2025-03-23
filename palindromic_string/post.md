回文字符串是一类常见的题目，首先我们要区分两个概念：
- 回文子串（palindromic substring），要求某个字符串s的连续的一个子串$s[i...j]$是回文串；
- 回文子序列（palindromic subsequence），不要求连续性，是通过对于字符串进行删减得到的序列，$s[i],s[j],s[k]，j - i >= 1$满足回文性质。

<!-- more -->

对于字符串的问题，常使用动态规划求解，一般有两种区间定义方式：
1. $dp[i][j], i <= j$, 即把s[i]到s[j]的部分定义成一个状态；
2. $dp[i], i >= 0$, 即把s[0]到s[i]的部分定义成一个状态，一般来说这种定义方式复杂度更低，因为区间的一端是固定的；

同时，对于第一种区间定义方法，一般有两种遍历方式：
1. 按照元素顺序遍历
```c++
    for (int i = 0; i < size; ++i) {
        for (int j = i; j < size; ++j) {
            ...
        }
    }
```
2. 按照区间长度遍历
```c++
    for (int len = 1; len <= size; ++len) {
        for (int i = 0; i + len - 1 < size; ++i) {
            int j = i + len - 1;
            ...
        }
    }
```

下面，我们在一些例题中来体会这些方法：


### [leetcode 5: Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
> Given a string s, return the longest palindromic substring in s.
> 
> Example 1:
> Input: s = "babad"
> Output: "bab"
> Note: "aba" is also a valid answer.
> 
> Example 2:
> Input: s = "cbbd"
> Output: "bb"
> 
> Example 3:
> Input: s = "a"
> Output: "a"
> 
> Example 4:
> Input: s = "ac"
> Output: "a"
>  
> Constraints:
> 1 <= s.length <= 1000
> s consist of only digits and English letters (lower-case and/or upper-case),

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        vector<vector<bool>> data(s.size(), vector<bool>(s.size(), false));
        
        int start = 0, end = 0;
        for (int len = 1; len <= s.size(); ++len) {
            for (int i = 0; i + len - 1 < s.size(); ++i) {
                if (len == 1) {
                    data[i][i] = true;
                    continue;
                }
                
                int j = i + len - 1;
                if (s[i] == s[j] && (j - 1 < i + 1 || data[i+1][j-1])) {
                    data[i][j] = true;
                    if (len > end - start + 1) {
                        start = i, end = j;
                    }
                }
            }
        }
        
        return s.substr(start, end - start + 1);
    }
};
```
上面是解法是基于DP的方案，即如果s[i...j]是回文子串，需要s[i] == s[j] 并且 s[i+1 .. j-1]也是回文子串；

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.size() <= 1) {
            return s;
        }
        
        int min_start = 0, max_len = 1;
        for (int i = 0; i < s.size();) {
            if (s.size() - i <= max_len / 2) {
                break;
            }
            
            int j = i, k = i;
            while (k < s.size() - 1 && s[k+1] == s[k]) {
                ++k; // Skip duplicate characters.
            }
            
            i = k+1;
            while (k < s.size() - 1 && j > 0 && s[k + 1] == s[j - 1]) {
                ++k;
                --j; 
            } // Expand.
            
            int new_len = k - j + 1;
            if (new_len > max_len) { 
                min_start = j; 
                max_len = new_len; 
            }
        }
        
        return s.substr(min_start, max_len);
    }
};
```
上面的解决方案尝试把每一个字符作为回文子串的中心，然后不断向外扩展。这里面用到了一个很重要的优化，即对于连续的重复字符，把它们当做一个整体考虑一次，而不是多次。

### [leetcode 516: Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)
> Given a string s, find the longest palindromic subsequence's length in s.
> 
> A subsequence is a sequence that can be derived from another sequence by deleting some or no elements > without changing the order of the remaining elements.
> 
> Example 1:
> Input: s = "bbbab"
> Output: 4
> Explanation: One possible longest palindromic subsequence is "bbbb".
> 
> Example 2:
> Input: s = "cbbd"
> Output: 2
> Explanation: One possible longest palindromic subsequence is "bb".
>  
> Constraints:
> 1 <= s.length <= 1000
> s consists only of lowercase English letters.

```c++
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        int size = s.size();
        vector<vector<int>> data(size, vector<int>(size, 0));
        
        for (int len = 1; len <= size; ++len) {
            for (int i = 0; i + len - 1 < size; ++i) {
                if (len == 1) {
                    data[i][i] = 1;
                    continue;
                }
                int j = i + len - 1;
                if (s[i] == s[j]) {
                    data[i][j] = (i + 1 <= j - 1? data[i+1][j-1]: 0) + 2;
                }
                data[i][j] = max({data[i][j], data[i+1][j], data[i][j-1]});
            }
        }
        
        return data.front().back();
    }
};
```
注意，这个题目要求子序列，而上一个题目是子串，DP的过程如14 - 17行所示；

### [leetcode 647: Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
> Given a string, your task is to count how many palindromic substrings in this string.
> 
> The substrings with different start indexes or end indexes are counted as different substrings even > they consist of same characters.
> 
> Example 1:
> Input: "abc"
> Output: 3
> Explanation: Three palindromic strings: "a", "b", "c".
>  
> 
> Example 2:
> Input: "aaa"
> Output: 6
> Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
>  
> 
> Note:
> The input string length won't exceed 1000.

```c++
class Solution {
public:
    int countSubstrings(string s) {
        int res = 0;
        for (int i = 0; i < s.size(); ++i) {
            int len = min<int>(i, s.size() - 1 - i);
            for (int j = 0; j <= len; ++j) { // odd length type:  ...a...
                if (s[i+j] == s[i - j]) {
                    ++res;
                } else {
                    break;
                }
            }
            
            if (i + 1 < s.size() && s[i] == s[i+1]) { // even length type: ...aa...
                len = min<int>(i, s.size() - 2 - i);
                for (int j = 0; j <= len; ++j) {
                    if (s[i+1+j] == s[i - j]) {
                        ++res;
                    } else {
                        break;
                    }
                }
            }
        }
        
        return res;
    }
};
```
这个题目要考虑到回文串的两种形式，即奇数长度形式：...a...和偶数长度形式：...aa...

### [leetcode 730: Count Different Palindromic Subsequences](https://leetcode.com/problems/count-different-palindromic-subsequences/)
> Given a string S, find the number of different non-empty palindromic subsequences in S, and return > that number modulo 10^9 + 7.
> 
> A subsequence of a string S is obtained by deleting 0 or more characters from S.
> 
> A sequence is palindromic if it is equal to the sequence reversed.
> 
> Two sequences A_1, A_2, ... and B_1, B_2, ... are different if there is some i for which A_i != B_i.
> 
> Example 1:
> Input: 
> S = 'bccb'
> Output: 6
> Explanation: 
> The 6 different non-empty palindromic subsequences are 'b', 'c', 'bb', 'cc', 'bcb', 'bccb'.
> Note that 'bcb' is counted only once, even though it occurs twice.
> 
> Example 2:
> Input: 
> S = 'abcdabcdabcdabcdabcdabcdabcdabcddcbadcbadcbadcbadcbadcbadcbadcba'
> Output: 104860361
> Explanation: 
> There are 3104860382 different non-empty palindromic subsequences, which is 104860361 modulo 10^9 + 7.
> 
> Note:
> The length of S will be in the range [1, 1000].
> Each character S[i] will be in the set {'a', 'b', 'c', 'd'}.

```c++
class Solution {
public:
    int countPalindromicSubsequences(string s) {
        long long mod = 1e9 + 7;
        long long res = 0;
        int size = s.size();
        vector<vector<long long>> data(size, vector<long long>(size, 0));
        for (int len = 1; len <= size; ++len) {
            for (int i = 0; i + len - 1 < size; ++i) {
                int j = i + len - 1;
                if (len <= 2) {
                    data[i][j] = len;
                } else {
                    for (char t : {'a', 'b', 'c', 'd'}) {
                        int m = i, n = j;
                        for (m = i; m <= j; ++m) {
                            if (s[m] == t) {
                                break;
                            }
                        }
                        for (n = j; n >= i; --n) {
                            if (s[n] == t) {
                                break;
                            }
                        }
                        
                        if (m > n) {
                            continue;
                        } else if (m == n) {
                            data[i][j] += 1;
                        } else if (m + 1 == n) {
                            data[i][j] += 2;
                        } else {
                            data[i][j] += 2 + data[m+1][n-1];
                        }
                    }
                }
                
                data[i][j] %= mod;
            }
        }
        
        return data.front().back();
    }
};
```
因为字符只有a,b,c,d四种，所以回文串一定是a...a, b...b, c...c和d...d的形式；所以对于每个区间[i ,j]，我们先寻找最外面的匹配字符对，有如下几种情况：
1. 如果没有找到（28行），那么添加0到区间中；
2. 如果只有一个（30行），那么这个字符只能给整个区间添加一个回文串
3. 如果只有相邻的两个（32行），那么这两个字符只能给整个区间添加两个回文串，如a,aa
4. 否则，可以带来如下的回文串，如(1) a, aa，共两个; (2) a {dp[m+1][n-1]} a，共dp[m+1][n-1]个

### [leetcode 87 Scramble String](https://leetcode.com/problems/scramble-string/)
> We can scramble a string s to get a string t using the following algorithm:
> 
> If the length of the string is 1, stop.
> If the length of the string is > 1, do the following:
> Split the string into two non-empty substrings at a random index, i.e., if the string is s, divide it > to x and y where s = x + y.
> Randomly decide to swap the two substrings or to keep them in the same order. i.e., after this step, s > may become s = x + y or s = y + x.
> Apply step 1 recursively on each of the two substrings x and y.
> Given two strings s1 and s2 of the same length, return true if s2 is a scrambled string of s1, > otherwise, return false.
> 
> Example 1:
> Input: s1 = "great", s2 = "rgeat"
> Output: true
> Explanation: One possible scenario applied on s1 is:
> "great" --> "gr/eat" // divide at random index.
> "gr/eat" --> "gr/eat" // random decision is not to swap the two substrings and keep them in order.
> "gr/eat" --> "g/r / e/at" // apply the same algorithm recursively on both substrings. divide at ranom > index each of them.
> "g/r / e/at" --> "r/g / e/at" // random decision was to swap the first substring and to keep the > second substring in the same order.
> "r/g / e/at" --> "r/g / e/ a/t" // again apply the algorithm recursively, divide "at" to "a/t".
> "r/g / e/ a/t" --> "r/g / e/ a/t" // random decision is to keep both substrings in the same order.
> The algorithm stops now and the result string is "rgeat" which is s2.
> As there is one possible scenario that led s1 to be scrambled to s2, we return true.
> 
> Example 2:
> Input: s1 = "abcde", s2 = "caebd"
> Output: false
> 
> Example 3:
> Input: s1 = "a", s2 = "a"
> Output: true
>  
> Constraints:
> s1.length == s2.length
> 1 <= s1.length <= 30
> s1 and s2 consist of lower-case English letters.

```c++
class Solution {
public:
    bool isScramble(string s1, string s2) {
        return isScramble(s1, 0, s1.size() - 1, s2, 0, s2.size() - 1);
    }
    
    
    bool isScramble(const string& s1, int a1, int b1, const string& s2, int a2, int b2) {        
        if (a1 == b1) {
            return s1[a1] == s2[a2];
        }
        if (b1 - a1 != b2 - a2) {
            return false;
        }
        
        vector<int> t1(26, 0), t2(26, 0);
        for (int i = a1; i <= b1; ++i) {
            ++t1[s1[i] - 'a'];
        }
        for (int i = a2; i <= b2; ++i) {
            ++t2[s2[i] - 'a'];
        }
        if (t1 != t2) {
            return false;
        }
        
        int n = b1 - a1 + 1;
        vector<int> l1(26, 0), r1(26, 0), l2(26, 0), r2(26, 0);
        for (int i = 0; i + 1 < n; ++i) {
            ++l1[s1[a1 + i] - 'a'];
            ++r1[s1[b1 - i] - 'a'];
            ++l2[s2[a2 + i] - 'a'];
            ++r2[s2[b2 - i] - 'a'];
            
            // 13, 24
            if (l1 == l2) {
                if (isScramble(s1, a1, a1 + i, s2, a2, a2 + i) && isScramble(s1, a1 + i + 1, b1, s2, a2 + i + 1, b2)) {
                    return true;
                }
            }

            // 14, 23
            if (l1 == r2) {
                if (isScramble(s1, a1, a1 + i, s2, b2 - i, b2) && isScramble(s1, a1 + i + 1, b1, s2, a2, b2 - i -1)) {
                    return true;
                }
            }
            
            // 23, 14
            if (l2 == r1) {
                if (isScramble(s1, b1 - i, b1, s2, a2, a2 + i) && isScramble(s1, a1, b1 - i - 1, s2, a2 + i +1, b2)) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

对于这个题目，可以将两个字符串分别分作两部分，然后注意交叉匹配的情况。


### 参考文献
- Leetcode算法题总结之区间dp， https://www.cnblogs.com/54hys/p/13505616.html
