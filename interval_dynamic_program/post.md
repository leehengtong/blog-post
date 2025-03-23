区间动态规划是很重要的一种动态规划类别，对于这种问题，要仔细分析理解题意，建立从小区间构造大区间的方法。下面我们以几个leetcode上面的题目为例给出分析思路和解析。在所有这些题目中，有一点往往是一样的：我们一般都是对最终的结果建立dp数组，也就是dp中的单个元素往往表示的是给定区间内的最优解，这一点在思考dp问题的时候尤为重要。

<!-- more -->

### [leetcode 312： Burst Balloons](https://leetcode.com/problems/burst-balloons/)

> You are given n balloons, indexed from 0 to n - 1. Each balloon is painted with a number on it represented by an array nums. You are asked to burst all the balloons.
>
> If you burst the ith balloon, you will get nums[i - 1] * nums[i] * nums[i + 1] coins. If i - 1 or i + 1 goes out of bounds of the array, then treat it as if there is a balloon with a 1 painted on it.
>
> Return the maximum coins you can collect by bursting the balloons wisely.
>
> Example 1:
>
> Input: nums = [3,1,5,8]
> Output: 167
> Explanation:
> nums = [3,1,5,8] --> [3,5,8] --> [3,8] --> [8] --> []
> coins =  3*1*5    +   3*5*8   +  1*3*8  + 1*8*1 = 167
>
> Example 2:
> Input: nums = [1,5]
> Output: 10
>
> Constraints:
> n == nums.length
> 1 <= n <= 500
> 0 <= nums[i] <= 100

```c++
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        vector<vector<int>> data(nums.size(), vector<int>(nums.size(), 0));
        for (int len = 1; len <= nums.size(); ++len) {
            for (int i = 0; i + len - 1 < nums.size(); ++i) {
                int j = i + len - 1;
                if (len == 1) {
                    data[i][i] = value(nums, i);
                    continue;
                }
                for (int k = i; k <= j; ++k) {
                    int left = 0;
                    int right = 0;
                    if (k > i) {
                        left = data[i][k-1];
                    }
                    if (k < j) {
                        right = data[k+1][j];
                    }
                    int mid = nums[k];
                    if (i - 1 >= 0) {
                        mid = mid * nums[i-1];
                    }
                    if (j + 1 < nums.size()) {
                        mid = mid * nums[j + 1];
                    }
                    int cur = left + mid + right;
                    data[i][j] = max(cur, data[i][j]);
                }
            }
        }

        return data.front().back();
    }

    int value(vector<int>& nums, int pos) {
        int res = nums[pos];
        if (pos - 1 >= 0) {
            res = res * nums[pos - 1];
        }
        if (pos + 1 < nums.size()) {
            res = res * nums[pos + 1];
        }
        return res;
    }
};
```
在这个题目中，我们使用data[i][j]表示在区间[i, j]所能获得的最大值。在捅破[i, j]区间的气球时候，我们先选择一个气球k最后捅破（当前最先捅破也可以，只不过处理方法不一样），然后这样就把一个大区间的问题分解成了两个小区间[i, k-1], [k+1, j]的问题。这里请注意，当捅破第k个气球的时候，它的邻居已经和原来不一样了。

### [leetcode 1547: Minimum Cost to Cut a Stick](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/)
> Given a wooden stick of length n units. The stick is labelled from 0 to n. For example, a stick of length 6 is labelled as follows:
>
> Given an integer array cuts where cuts[i] denotes a position you should perform a cut at.
>
> You should perform the cuts in order, you can change the order of the cuts as you wish.
>
> The cost of one cut is the length of the stick to be cut, the total cost is the sum of costs of all cuts. When you cut a stick, it will be split into two smaller sticks (i.e. the sum of their lengths is the > length of the stick before the cut). Please refer to the first example for a better explanation.
>
> Return the minimum total cost of the cuts.

```c++
class Solution {
public:
    int minCost(int n, vector<int>& cuts) {
        cuts.push_back(0);
        cuts.push_back(n);
        sort(cuts.begin(), cuts.end());
        vector<vector<int>> data(cuts.size(), vector<int>(cuts.size(), -1));

        for (int cut_num = 2; cut_num <= cuts.size(); ++cut_num) {
            for (int start_cut_idx = 0; start_cut_idx + cut_num - 1 < cuts.size(); ++start_cut_idx) {
                int end_cut_idx = start_cut_idx + cut_num - 1;
                if (cut_num == 2) {
                    data[start_cut_idx][end_cut_idx] = 0;
                } else {
                    int len = cuts[end_cut_idx] - cuts[start_cut_idx];
                    for (int cur_cut_idx = start_cut_idx + 1; cur_cut_idx < end_cut_idx; ++cur_cut_idx) {
                        if (data[start_cut_idx][end_cut_idx] == -1) {
                            data[start_cut_idx][end_cut_idx] = data[start_cut_idx][cur_cut_idx] + len + data[cur_cut_idx][end_cut_idx];
                        } else {
                            data[start_cut_idx][end_cut_idx] = min(data[start_cut_idx][end_cut_idx], data[start_cut_idx][cur_cut_idx] + len + data[cur_cut_idx][end_cut_idx]);
                        }
                    }
                }
            }
        }

        return data[0].back();
    }
};
```
对于这个题目，我们使用data[start_cut_idx][end_cut_idx]表示从第start_cut_idx个砍断位置到第end_cut_idx个砍断位置的最小cost. 和前面比较类似，在处理区间[start_cut_idx, end_cut_idx]的时候，我们也是不断尝试改变下刀位置，从而将大区间的问题分解成两个小区间的问题； 不过这里有一点需要注意，我们把0和n也当做一个砍断位置来处理，这样比较简单。

### [leetcode 1000: Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
> There are N piles of stones arranged in a row.  The i-th pile has stones[i] stones.
>
> A move consists of merging exactly K consecutive piles into one pile, and the cost of this move is equal to the total number of stones in these K piles.
>
> Find the minimum cost to merge all piles of stones into one pile.  If it is impossible, return -1.
>
> Example 1:
> Input: stones = [3,2,4,1], K = 2
> Output: 20
> Explanation:
> We start with [3, 2, 4, 1].
> We merge [3, 2] for a cost of 5, and we are left with [5, 4, 1].
> We merge [4, 1] for a cost of 5, and we are left with [5, 5].
> We merge [5, 5] for a cost of 10, and we are left with [10].
> The total cost was 20, and this is the minimum possible.
>
> Example 2:
> Input: stones = [3,2,4,1], K = 3
> Output: -1
> Explanation: After any merge operation, there are 2 piles left, and we can't merge anymore.  So the task is impossible.
>
> Example 3:
> Input: stones = [3,5,1,2,6], K = 3
> Output: 25
> Explanation:
> We start with [3, 5, 1, 2, 6].
> We merge [5, 1, 2] for a cost of 8, and we are left with [3, 8, 6].
> We merge [3, 8, 6] for a cost of 17, and we are left with [17].
> The total cost was 25, and this is the minimum possible.
>
> Note:
> 1 <= stones.length <= 30
> 2 <= K <= 30
> 1 <= stones[i] <= 100

```c++
class Solution {
public:
    int mergeStones(vector<int>& stones, int K) {
        const int n = stones.size();
        if ((n - K) % (K - 1))
            return -1;
        // calculate the sum from 0 to i
        vector<int> sumStones(n + 1, 0);
        for (auto i = 0; i < n; ++i) {
            sumStones[i + 1] = sumStones[i] + stones[i];
        }
        // initialize the 3d vector dp
        vector<vector<vector<int>>> dp(n, vector<vector<int>>(n, vector<int>(K + 1, 1e9)));
        for (int i = 0; i < n; ++i) {
            dp[i][i][1] = 0;
        }
        // compute the transition functions
        for (auto stepLen = 2; stepLen <= n; ++stepLen) {
            for (auto i = 0; i <= n - stepLen; ++i) {
                int j = i + stepLen - 1;
                for (auto k = 2; k <= K; k++) {
                    for ( auto m = i; m < j; m += K - 1) {
                        dp[i][j][k] = min(dp[i][j][k], dp[i][m][1] + dp[m + 1][j][k - 1]);
                    }
                }
                dp[i][j][1] = dp[i][j][K] + sumStones[j + 1] - sumStones[i];
            }
        }
        return dp[0][n - 1][1];
    }
};
```
这道题首先让人想到哈夫曼编码，但是请注意"merging exactly K consecutive piles into one pile"，所以这个题目并不是一个哈夫曼编码的题目。
这这道题中，我们使用三维状态dp[i][j][k]表示把从[i, j]的石头合并成k堆的最小代价。在22-24行里面，我们尝试进行多次merge是的最终的个数是k。因为对于每一个区间我们都尝试进行多次merge，所以dp[i][j][k]表示的是把[i,j]中的石头通过各种方法merge成k的最小cost.


### [leetcode 813: Largest Sum of Averages](https://leetcode.com/problems/largest-sum-of-averages/)
> We partition a row of numbers A into at most K adjacent (non-empty) groups, then our score is the sum of the average of each group. What is the largest score we can achieve?
>
> Note that our partition must use every number in A, and that scores are not necessarily integers.
>
> Example:
> Input:
> A = [9,1,2,3,9]
> K = 3
> Output: 20
> Explanation:
> The best choice is to partition A into [9], [1, 2, 3], [9]. The answer is 9 + (1 + 2 + 3) / 3 + 9 = 20.
> We could have also partitioned A into [9, 1], [2], [3, 9], for example.
> That partition would lead to a score of 5 + 2 + 6 = 13, which is worse.
>
> Note:
>
> 1 <= A.length <= 100.
> 1 <= A[i] <= 10000.
> 1 <= K <= A.length.
> Answers within $10^{-6}$ of the correct answer will be accepted as correct.

```c++
class Solution {
public:
    double largestSumOfAverages(vector<int>& A, int k) {
        vector<vector<double>> data(A.size(), vector<double>(k + 1, -1));

        vector<int> sum(A);
        for (int i = 1; i < A.size(); ++i) {
            sum[i] += sum[i - 1];
        }

        for (int i = 0; i < A.size(); ++i) {
            for (int j = 1; j <= k; ++j) {
                if (j == 1) {
                    data[i][j] = sum[i] * 1.0 / (i + 1);
                } else {
                    for (int t = 0; t < i; ++t) {
                        if (data[t][j-1] != -1) {
                            data[i][j] = max(data[i][j], data[t][j-1] + (sum[i] - sum[t]) * 1.0 / (i - t));
                        }
                    }
                }
            }
        }

        return data.back().back();
    }
};
```
和前面的几个问题不同，在这个题目中，我们操作的区间的起点是固定的，这也是一种常见的dp方式。我们使用data[i][j]表示把[0, i]中的数字分成j组所获得的最大值。在第18行迭代的过程中，我们先把前t个元素分成j-1组，然后再把[t+1, i]的元素分到第j组。


### [leetcode 87: Scramble String](https://leetcode.com/problems/scramble-string/)
> We can scramble a string s to get a string t using the following algorithm:
>
> If the length of the string is 1, stop.
> If the length of the string is > 1, do the following:
> Split the string into two non-empty substrings at a random index, i.e., if the string is s, divide it to x and y where s = x + y.
> Randomly decide to swap the two substrings or to keep them in the same order. i.e., after this step, s may become s = x + y or s = y + x.
> Apply step 1 recursively on each of the two substrings x and y.
> Given two strings s1 and s2 of the same length, return true if s2 is a scrambled string of s1, otherwise, return false.
>
> Example 1:
> Input: s1 = "great", s2 = "rgeat"
> Output: true
> Explanation: One possible scenario applied on s1 is:
> "great" --> "gr/eat" // divide at random index.
> "gr/eat" --> "gr/eat" // random decision is not to swap the two substrings and keep them in order.
> "gr/eat" --> "g/r / e/at" // apply the same algorithm recursively on both substrings. divide at ranom index each of them.
> "g/r / e/at" --> "r/g / e/at" // random decision was to swap the first substring and to keep the second substring in the same order.
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

这个题目比较好理解，我们把s1的前k个元素构成的子串叫做l1, 后k个叫做r1; 对于s2来说，l2和r2同理。所以一共有三种匹配的情况：（1）l1和l2匹配，头头匹配；（2）l1和r2匹配，头尾匹配；（3）r1和l2匹配，尾头匹配。因为我们会先确保s1和s2由相同的元素构成，所以在上面这三种情况下，两个字符串余下的部分自然是可以匹配的。基于这个思想，我们先给出递归算法：
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

对于这个题目，定义动态规划数组确相当不容易，因为在任何一步都是可以调换切分前后部分的。这里，我们定义dp[i][j][k]为s1中的[i, i + k - 1]和s2中的[j, j + k - 1]是否匹配。这个定义非常巧妙，它可以表示s1中任意的[a, b]和s2中的[c,d]的匹配关系。在最直接的思想下，要想表示两个任意的区间需要四维状态，但是因为我们有一个额外的约束：两个区间的长度是一样的，所以我们使用三维数组表示了这个状态。
```c++
class Solution {
public:
    bool isScramble(string s1, string s2) {
        if (s1.size() != s2.size()) return false;
        if (s1 == s2) return true;
        int n = s1.size();
        vector<vector<vector<bool>>> dp (n, vector<vector<bool>>(n, vector<bool>(n + 1)));
        for (int len = 1; len <= n; ++len) {
            for (int i = 0; i <= n - len; ++i) {
                for (int j = 0; j <= n - len; ++j) {
                    if (len == 1) {
                        dp[i][j][1] = s1[i] == s2[j];
                    } else {
                        for (int k = 1; k < len; ++k) {
                            if ((dp[i][j][k] && dp[i + k][j + k][len - k]) || (dp[i + k][j][len - k] && dp[i][j + len - k][k])) {
                                dp[i][j][len] = true;
                            }
                        }
                    }
                }
            }
        }
        return dp[0][0][n];
    }
};
```

### [leetcode 546: Remove Boxes](https://leetcode.com/problems/remove-boxes/)
> You are given several boxes with different colors represented by different positive numbers.
>
> You may experience several rounds to remove boxes until there is no box left. Each time you can choose some continuous boxes with the same color (i.e., composed of k boxes, k >= 1), remove them and get k * k > points.
>
> Return the maximum points you can get.
>
> Example 1:
> Input: boxes = [1,3,2,2,2,3,4,3,1]
> Output: 23
> Explanation:
> [1, 3, 2, 2, 2, 3, 4, 3, 1]
> ----> [1, 3, 3, 4, 3, 1] (3*3=9 points)
> ----> [1, 3, 3, 3, 1] (1*1=1 points)
> ----> [1, 1] (3*3=9 points)
> ----> [] (2*2=4 points)
>
> Example 2:
> Input: boxes = [1,1,1]
> Output: 9
>
> Example 3:
> Input: boxes = [1]
> Output: 1
>
> Constraints:
> 1 <= boxes.length <= 100
> 1 <= boxes[i] <= 100

```c++
class Solution {
public:
    int removeBoxes(vector<int>& boxes) {
        int n = boxes.size();
        vector<vector<vector<int>>> data(n, vector<vector<int>>(n, vector<int>(n + 1, -1)));

        for (int len = 1; len <= boxes.size(); ++len) {
            for (int i = 0; i + len - 1 < boxes.size(); ++i) {
                int j = i + len - 1;
                for (int k = 1; k <= boxes.size() - j; ++k) {
                    if (i == j) {
                        data[i][j][k] = k * k;
                    } else {
                        data[i][j][k] = k * k + data[i][j-1][1];
                    }

                    for (int t = j - 1; t >= i; --t) {
                        if (boxes[t] == boxes[j] && data[i][t][k+1] != -1) {
                            if (t + 1 > j - 1) {
                                data[i][j][k] = max(data[i][j][k], data[i][t][k+1]);
                            } else {
                                data[i][j][k] = max(data[i][j][k], data[t+1][j-1][1] + data[i][t][k+1]);
                            }
                        }
                    }
                }
            }
        }

        return data[0].back()[1];
    }
};
```

这个题目和前面的题目又有一些差别。这里，我们使用data[i][j][k]表示在区间[i,j]后面有k个连续元素和boxes[j]相同时候所能获得的最大值。这样，我们的最终目标就是是data[0][size-1][1]。因为我们仅仅指定区间范围是不够的，我们还需要知道这时候我们累计了多少个连续的元素，以便在当前区间处理。这里，我们统计累积的和区间末尾相同的元素个数，当然也可以用其他方案来表示这个累计值。


### 参考文献
- Leetcode算法题总结之区间dp， https://www.cnblogs.com/54hys/p/13505616.html
- acwing, https://www.acwing.com/solution/LeetCode/content/1127/
