---
title: DP常见种类
date: 2020-03-04 10:18:08
categories:
- [算法, DP]
tags:
- 编程
---

# DP常见种类

* 对于dp数组下标什么时候从1开始, 假如状态中有 `i-1`转移过来的就最好从1开始. 这样可以给dp[0]初值, 设置边界, 少一些判断
* 一般解决 **最值, 计数, 存在性**
* 时间复杂度: 状态数 * 转移代价   (定义状态的个数 * 每个状态计算的代价)

## 术语 & 满足性质

### 术语

- 动态规划：运筹学中的一个分支，是求解决策过程最优化的数学方法。
- 阶段：把所给求解问题的过程恰当地分成若干个相互联系的阶段。
- 状态：状态表示每个阶段开始面临的自然状况或客观条件。
- 决策（转移）：一个阶段的状态给定以后，从该状态演变到下一阶段某个状态的一种选择称为决策。
- 策略：每阶段都做一个决策，一系列决策的集合。
- 边界：初始集合。

### 满足性质

- 最优子结构：一个最优化策略的子策略总是最优的，反过来，我们可以通过最优的子策略，推出最优策略。
- 无后效性：当我们通过一系列策略到达了某一阶段的某一状态时，下一步决策不受之前的一系列策略影响，仅由当前状态决定。
- 子问题重叠：算法计算的过程中会反复地求解相同的一定量的子问题，而不是不断生成没有见过的新问题。也就是说子问题空间不大，或是状态空间不大，我们可以通过存储状态的答案加快计算速度。

### 记忆化搜索模板

* wnjxyk大佬讲模板

```c++
int dp[状态表示];
int dfs(状态表示) {
    if (决策边界) return 决策边界答案; // 决策边界
    
    if (dp[状态表示] != 无效数值) return dp[状态表示]; // 记忆化
    
    for (当前状态表示的 子状态) dfs(子状态) 更新 dp[状态表示]; // 状态转移
    return dp[状态表示];
}
int solve() {
    memset(dp, 无效数值, sizeof(dp));
    return dfs(原问题状态);
}
```



## 线性DP （序列型）

* 递推方程有一个明显的线性关系, 背包问题也是线性DP的一种
* 线性DP的状态可以想象成一个n维矩阵, 一行一行按着顺序求出结果, 这种DP问题叫做线性DP
* 常见问题: 背包问题, 数字三角形(lc120), 最长上升子序列(lc300), 最长公共子序列(lc1143), 编辑距离(lc72)



## 区间DP

* 状态是用区间表示
* 经典问题: 石子合并(ac282)

