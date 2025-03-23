模拟退火是一种通用概率算法，常用来在一定时间内寻找在一个很大搜寻空间中的近似最优解。和爬山算法相比，模拟退火算法以一定的概率接受不好的迭代，从而跳出局部最优。

下面我们尝试使用模拟退火算法来解决[上篇文章](https://leehengtong.com/2020/12/07/optimization_problem/)中的几个问题：

<!-- more -->

## 问题1: 离散空间优化问题
我们使用以下程序进行求解：
```python
#!/usr/bin/python3

import random
import math


def neighbor(last, T, t):
    """
    returns a neighbor
    """
    # compute used time
    time = lambda cur: sum([cur[i] * t[i] for i in range(M)])

    while True:
        res = [i for i in last]
        idx = random.randint(0, len(last) - 1)
        res[idx] = 1 - res[idx]
        if time(res) <= T:
            return res


def solve(T, M, t, m):
    # compute the value got
    value = lambda cur: sum([cur[i] * m[i] for i in range(M)])

    # temperature setting
    cur_tmp = 100
    tmp_fin = 0.01
    # valid init solution
    ans = [0, 0, 0]
    val = value(ans)
    while cur_tmp > tmp_fin:
        # get a neighbor
        next = neighbor(ans, T, t)
        next_value = value(next)

        diff = next_value - val
        r = random.random()
        if next_value > val or r < math.exp(-abs(diff) / cur_tmp):
            ans = next
            val = next_value

        # update the temperature
        cur_tmp = cur_tmp * 0.999

    print(ans)


if __name__ == "__main__":
    T = 70
    M = 3
    t = [71, 69, 1]
    m = [100, 1, 2]

    solve(T, M, t, m)
```

输出结果：
```
[0, 1, 1]
```


## 问题2: 连续空间多变量多约束非线性规划问题

我们使用以下程序进行求解：
```python
#!/usr/bin/python3

import random
import math


def constrain(x):
    """
    check the constraint
    """
    for i in x:
        if i > 6 or i < -6 or i > 10 or i < -10:
            return False

    if -x[0] * x[1] > 10:
        return False

    if 1.5 + x[0] * x[1] - x[0] - x[1] > 0:
        return False

    return True


def neighbor(x):
    """
    returns a neighbor
    """
    while True:
        res = [i for i in x]
        r1 = random.random()

        idx = 0
        if r1 > 0.5:
            idx = 1

        r2 = random.random()
        sign = 1
        if r2 > 0.5:
            sign = -1

        r3 = random.random()
        res[idx] = res[idx] + sign * r3

        if constrain(res):
            return res


def func(x):
    """
    the target function
    """
    return math.exp(
        x[0]) * (4 * x[0]**2 + 2 * x[1]**2 + 4 * x[0] * x[1] + 2 * x[1] + 1)


def solve():
    # temperature setting
    cur_tmp = 100
    tmp_fin = 0.01
    # valid init solution
    ans = [0, 5]
    val = func(ans)
    while cur_tmp > tmp_fin:
        # get a neighbor
        next = neighbor(ans)
        next_value = func(next)

        diff = next_value - val
        r = random.random()
        if next_value < val or r < math.exp(-abs(diff) / cur_tmp):
            ans = next
            val = next_value

        # update the temperature
        cur_tmp = cur_tmp * 0.999

    print(ans)
    print(func(ans))


if __name__ == "__main__":
    solve()
```
输出结果：
```
[-5.976946429765198, 1.5103517825203914]
0.2926414515330139
```
用相同的初始值，模拟退火算法得到了更好的结果。




## 问题3: 连续空间最值问题

我们使用以下程序进行求解：
```python
#!/usr/bin/python3

import random
import math


def func(x):
    """
    the target function
    """
    return -200 * math.exp(-0.05 * x[0]) * math.sin(x[0])


def constrain(x):
    """
    check the constraint
    """
    return x[0] >= -2 and x[0] <= 2


def neighbor(x):
    """
    returns a neighbor
    """
    while True:
        res = [i for i in x]

        r1 = random.random()
        sign = 1
        if r1 > 0.5:
            sign = -1

        r2 = random.random()
        res[0] = res[0] + sign * r2

        if constrain(res):
            return res


def solve():
    # temperature setting
    cur_tmp = 100
    tmp_fin = 0.01
    # valid init solution
    ans = [0]
    val = func(ans)
    while cur_tmp > tmp_fin:
        # get a neighbor
        next = neighbor(ans)
        next_value = func(next)

        diff = next_value - val
        r = random.random()
        if next_value < val or r < math.exp(-abs(diff) / cur_tmp):
            ans = next
            val = next_value

        # update the temperature
        cur_tmp = cur_tmp * 0.999

    print(ans)
    print(func(ans))


if __name__ == "__main__":
    solve()
```

输出结果：
```
[1.530673326410524]
-185.11524120724246
```


## 参考文献
1. https://zh.wikipedia.org/wiki/%E6%A8%A1%E6%8B%9F%E9%80%80%E7%81%AB
2. https://en.wikipedia.org/wiki/Simulated_annealing

