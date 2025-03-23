爬山算法是一种局部择优的方法，采用启发式方法，是对深度优先搜索的一种改进，它利用反馈信息帮助生成解的决策。

爬山算法一般存在以下问题：
1. 局部最大
2. 高地：也称为平顶，搜索一旦到达高地，就无法确定搜索最佳方向，会产生随机走动，使得搜索效率降低。
3. 山脊：搜索可能会在山脊的两面来回震荡，前进步伐很小。

<!-- more -->

对于离散问题，我们从当前解出发，找到最佳的邻居进行下一步的迭代；对于连续空间的问题，我们对于每一个分量按照一定的步长进行探索，如果找到了更优的邻居，我们更新下一轮的步长为当前的最优步长；否则我们减小步长。下面我们尝试使用爬山算法来解决[上篇文章](https://leehengtong.com/2020/12/07/optimization_problem/)中的几个问题：

## 问题1: 离散空间优化问题
我们使用以下程序进行求解：
```python
#!/usr/bin/python3


def neighbor(last):
    """
    returns all neighbors
    """
    for i in range(M):
        cur = []
        for j, n in enumerate(last):
            if j == i:
                n = 1 - n
            cur.append(n)

        yield cur


def solve(T, M, t, m):
    # compute used time
    time = lambda cur: sum([cur[i] * t[i] for i in range(M)])
    # compute the value got
    value = lambda cur: sum([cur[i] * m[i] for i in range(M)])

    # valid init solution
    ans = [0, 0, 0]
    update = True
    while update:
        update = False
        ans_time = time(ans)
        ans_value = value(ans)

        for cur in neighbor(ans):
            cur_time = time(cur)
            cur_value = value(cur)

            if cur_time <= T and cur_value > ans_value:
                ans = cur
                update = True

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

import math
import copy


def func(x):
    """
    the target function
    """
    return math.exp(
        x[0]) * (4 * x[0]**2 + 2 * x[1]**2 + 4 * x[0] * x[1] + 2 * x[1] + 1)


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


def solve():
    step = [0.1, 0.1]
    accel = 1.2
    candi = [-accel, -1 / accel, 1 / accel, accel]

    # valid init solution
    cur_ans = [0, 5]
    best_val = func(cur_ans)
    k = 0

    while True:
        k = k + 1
        before_val = best_val
        for i, x in enumerate(cur_ans):
            before_ans = copy.deepcopy(cur_ans)
            best_step = 0
            for c in candi:
                cur_step = step[i] * c
                cur_ans[i] = before_ans[i] + cur_step
                if constrain(cur_ans):
                    cur_val = func(cur_ans)
                    if cur_val < best_val:
                        print(
                            "update {0}th by step {1}, current ans: {2}, current val: {3})"
                            .format(i, cur_step, cur_ans, cur_val))
                        best_val = cur_val
                        best_step = cur_step

            if best_step == 0:
                step[i] = step[i] / accel
                cur_ans[i] = before_ans[i]
            else:
                step[i] = best_step
                cur_ans[i] = before_ans[i] + best_step

        print("iteration {0}: {1}, {2}".format(k, cur_ans, best_val))
        if abs(best_val - before_val) < 1e-16:
            break

    print(cur_ans)
    print(func(cur_ans))


if __name__ == "__main__":
    solve()
```
输出结果：
```
update 0th by step -0.12, current ans: [-0.12, 5], current val: 52.02462420878034)
update 1th by step -0.12, current ans: [-0.12, 4.88], current val: 49.759784181579406)
iteration 1: [-0.12, 4.88], 49.759784181579406
update 0th by step -0.1, current ans: [-0.22, 4.88], current val: 43.56714050378704)
update 0th by step -0.144, current ans: [-0.264, 4.88], current val: 41.09756946514502)
update 1th by step -0.1, current ans: [-0.264, 4.78], current val: 39.541347884384685)
update 1th by step -0.144, current ans: [-0.264, 4.736], current val: 38.86634214954465)
iteration 2: [-0.264, 4.736], 38.86634214954465
update 0th by step -0.12, current ans: [-0.384, 4.736], current val: 33.13480982120382)
update 0th by step -0.17279999999999998, current ans: [-0.43679999999999997, 4.736], current val: 30.896422546501622)
update 1th by step -0.12, current ans: [-0.43679999999999997, 4.616], current val: 29.426662476165006)
update 1th by step -0.17279999999999998, current ans: [-0.43679999999999997, 4.5632], current val: 28.79175788291818)
iteration 3: [-0.43679999999999997, 4.5632], 28.79175788291818
update 0th by step -0.144, current ans: [-0.5808, 4.5632], current val: 23.787883625501976)
update 0th by step -0.20735999999999996, current ans: [-0.64416, 4.5632], current val: 21.883178378702226)
update 1th by step -0.144, current ans: [-0.64416, 4.4192], current val: 20.56837447029764)
update 1th by step -0.20735999999999996, current ans: [-0.64416, 4.355840000000001], current val: 20.00365871483697)
iteration 4: [-0.64416, 4.355840000000001], 20.00365871483697
update 0th by step -0.17279999999999998, current ans: [-0.8169599999999999, 4.355840000000001], current val: 15.945283197922732)
update 0th by step -0.24883199999999994, current ans: [-0.8929919999999999, 4.355840000000001], current val: 14.448411201985614)
update 1th by step -0.17279999999999998, current ans: [-0.8929919999999999, 4.183040000000001], current val: 13.351387037399203)
update 1th by step -0.24883199999999994, current ans: [-0.8929919999999999, 4.107008], current val: 12.884188535334253)
iteration 5: [-0.8929919999999999, 4.107008], 12.884188535334253
update 0th by step -0.20735999999999996, current ans: [-1.1003519999999998, 4.107008], current val: 9.887958052713891)
update 0th by step -0.29859839999999993, current ans: [-1.1915904, 4.107008], current val: 8.824528435241017)
update 1th by step -0.20735999999999996, current ans: [-1.1915904, 3.8996480000000004], current val: 7.990194786653624)
update 1th by step -0.29859839999999993, current ans: [-1.1915904, 3.8084096000000005], current val: 7.639637828810663)
iteration 6: [-1.1915904, 3.8084096000000005], 7.639637828810663
update 0th by step -0.24883199999999994, current ans: [-1.4404223999999999, 3.8084096000000005], current val: 5.679407582979268)
update 0th by step -0.3583180799999999, current ans: [-1.5499084799999998, 3.8084096000000005], current val: 5.0143729160083135)
update 1th by step -0.24883199999999994, current ans: [-1.5499084799999998, 3.559577600000001], current val: 4.457854774352143)
update 1th by step -0.3583180799999999, current ans: [-1.5499084799999998, 3.4500915200000004], current val: 4.229641649646535)
iteration 7: [-1.5499084799999998, 3.4500915200000004], 4.229641649646535
update 0th by step -0.29859839999999993, current ans: [-1.8485068799999997, 3.4500915200000004], current val: 3.128071004768359)
update 0th by step -0.4299816959999999, current ans: [-1.9798901759999996, 3.4500915200000004], current val: 2.7704007740632144)
update 1th by step -0.29859839999999993, current ans: [-1.9798901759999996, 3.1514931200000005], current val: 2.470084617885889)
update 1th by step -0.4299816959999999, current ans: [-1.9798901759999996, 3.0201098240000004], current val: 2.3535469510854665)
iteration 8: [-1.9798901759999996, 3.0201098240000004], 2.3535469510854665
update 0th by step -0.3583180799999999, current ans: [-2.3382082559999997, 3.0201098240000004], current val: 1.8243045861337608)
update 0th by step -0.5159780351999999, current ans: [-2.4958682111999995, 3.0201098240000004], current val: 1.6525025525118668)
update 1th by step -0.3583180799999999, current ans: [-2.4958682111999995, 2.6617917440000003], current val: 1.5526668064303875)
update 1th by step -0.5159780351999999, current ans: [-2.4958682111999995, 2.5041317888000005], current val: 1.5221494499076038)
iteration 9: [-2.4958682111999995, 2.5041317888000005], 1.5221494499076038
update 0th by step -0.4299816959999999, current ans: [-2.9258499071999995, 2.5041317888000005], current val: 1.2592527050521631)
update 0th by step -0.6191736422399998, current ans: [-3.1150418534399993, 2.5041317888000005], current val: 1.160966703528361)
iteration 10: [-3.1150418534399993, 2.5041317888000005], 1.160966703528361
update 0th by step -0.5159780351999999, current ans: [-3.631019888639999, 2.5041317888000005], current val: 0.9249119591204455)
update 0th by step -0.7430083706879997, current ans: [-3.8580502241279993, 2.5041317888000005], current val: 0.8326186761463709)
iteration 11: [-3.8580502241279993, 2.5041317888000005], 0.8326186761463709
iteration 12: [-3.8580502241279993, 2.5041317888000005], 0.8326186761463709
[-3.8580502241279993, 2.5041317888000005]
0.8326186761463709
```




## 问题3: 连续空间最值问题

我们使用以下程序进行求解：
```python
#!/usr/bin/python3

import math
import copy


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


def solve():
    step = [0.1]
    accel = 1.2
    candi = [-accel, -1 / accel, 1 / accel, accel]

    # valid init solution
    cur_ans = [0]
    best_val = func(cur_ans)
    k = 0

    while True:
        k = k + 1
        before_val = best_val
        for i, x in enumerate(cur_ans):
            before_ans = copy.deepcopy(cur_ans)
            best_step = 0
            for c in candi:
                cur_step = step[i] * c
                cur_ans[i] = before_ans[i] + cur_step
                if constrain(cur_ans):
                    cur_val = func(cur_ans)
                    if cur_val < best_val:
                        print(
                            "update {0}th by step {1}, current ans: {2}, current val: {3})"
                            .format(i, cur_step, cur_ans, cur_val))
                        best_val = cur_val
                        best_step = cur_step

            if best_step == 0:
                step[i] = step[i] / accel
                cur_ans[i] = before_ans[i]
            else:
                step[i] = best_step
                cur_ans[i] = before_ans[i] + best_step

        print("iteration {0}: {1}, {2}".format(k, cur_ans, best_val))
        if abs(best_val - before_val) < 1e-16:
            break

    print(cur_ans)
    print(func(cur_ans))


if __name__ == "__main__":
    solve()
```
输出结果：
```
update 0th by step 0.08333333333333334, current ans: [0.08333333333333334], current val: -16.578163451266125)
update 0th by step 0.12, current ans: [0.12], current val: -23.799216912346857)
iteration 1: [0.12], -23.799216912346857
update 0th by step 0.1, current ans: [0.22], current val: -43.16845036828165)
update 0th by step 0.144, current ans: [0.264], current val: -51.5044434420067)
iteration 2: [0.264], -51.5044434420067
update 0th by step 0.12, current ans: [0.384], current val: -73.50156515472206)
update 0th by step 0.17279999999999998, current ans: [0.43679999999999997], current val: -82.78060135028912)
iteration 3: [0.43679999999999997], -82.78060135028912
update 0th by step 0.144, current ans: [0.5808], current val: -106.59760552432024)
update 0th by step 0.20735999999999996, current ans: [0.64416], current val: -116.29867444926786)
iteration 4: [0.64416], -116.29867444926786
update 0th by step 0.17279999999999998, current ans: [0.8169599999999999], current val: -139.97751214953558)
update 0th by step 0.24883199999999994, current ans: [0.8929919999999999], current val: -148.98732425593082)
iteration 5: [0.8929919999999999], -148.98732425593082
update 0th by step 0.20735999999999996, current ans: [1.1003519999999998], current val: -168.73015014835786)
update 0th by step 0.29859839999999993, current ans: [1.1915904], current val: -175.04569315355005)
iteration 6: [1.1915904], -175.04569315355005
update 0th by step 0.24883199999999994, current ans: [1.4404223999999999], current val: -184.52286412601495)
update 0th by step 0.3583180799999999, current ans: [1.5499084799999998], current val: -185.04587653587126)
iteration 7: [1.5499084799999998], -185.04587653587126
iteration 8: [1.5499084799999998], -185.04587653587126
[1.5499084799999998]
-185.04587653587126
```


## 参考文献
1. https://oi-wiki.org/misc/hill-climbing/
2. https://en.wikipedia.org/wiki/Hill_climbing
