## 定义
$$ min \ f_{0}(x) $$
$$ subject \ to \ f_{i}(x) \leqslant b_{i} \ i=1,...,m $$

- 目标函数和限制函数都是凸的，即
$$ f_{i}(\alpha x + \beta y) \leqslant \alpha f_{i}(x) + \beta f_{i}(y) \ \alpha + \beta = 1, \alpha \geq 0, \beta \geq 0 $$
- 最小二乘问题和线性规划问题都是凸优化问题的特例

<!-- more -->

## 特性
1. 没有解析解
2. 但是有可靠和高效的求解算法
3. 计算时间复杂度大体上和$max\(n^{3}, n^{2}m, F\)$成正比，其中$F$是计算$f_{i}$和它的一阶导，二阶导的开销

## 性质
1. 每一个局部最优解都是全局最优解
2. 最优解集合是凸的
3. 如果目标函数是严格凸的，那么这个优化问题就最多只有一个最优解

## 使用
1. 一般不容易分析出问题的凸优化形式
2. 需要很多技巧才能将问题转换成凸优化问题
3. 会对很多问题能够凸优化求解感到惊奇

## 参考文献
1. Standford Boyd, Convex Optimization course, https://web.stanford.edu/~boyd/cvxbook/
2. Standford Boyd, Convex Optimization [slides](https://web.stanford.edu/~boyd/cvxbook/bv_cvxslides.pdf)

