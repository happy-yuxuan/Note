# 背包问题

* v为体积， w为价值 `v, w > 0 `

## 0-1背包

```
dp(i, j) 表示选前i件物品到背包容量为j的最大价值
```

```
dp(i, j) = max(dp(i-1, j), dp(i-1, j-v[i]) + w[i])
```

* 转移方程解释：选前i件物体的背包容量为j的最大价值由两个状态转移过来： case1： 不选第i件物品，只在前i-1件物品中选最大价值； case2：选第i件物品，那前i-1件物品中只能选j-v[i]容量的价值。

* base case：`dp(0, 0) = 0`选前0件物品到容量为j的最大价值为0
* 对于每个阶段i，求出对应背包容量[0, V]的最大价值

#### 暴力

```c++
// 返回选择前i件物品在背包为j容量的最大价值
int dfs(int i, int j) {
    if (i == 0) return 0;
    
    int res = dfs(i - 1, j);
    if (j >= v[i]) res = max(res, dfs(i - 1, j - v[i]) + w[i]);
    
    return res;
}
```

#### 记忆化搜索

```c++
// 返回选择前i件物品在背包为j容量的最大价值
// memo 为 (n + 1) * (V + 1)的矩阵，初试值为-1
int dfs(int i, int j, vector<vector<int>>& memo) { 
    if (i == 0) return 0;
    
    if (memo[i-1][j] == -1) memo[i-1][j] = dfs(i - 1, j, memo);
    if (j >= v[i] && memo[i-1][j-v[i]] == -1) memo[i-1][j-v[i]] = dfs(i - 1, j - v[i], memo);

    memo[i][j] = memo[i-1][j];
    if (j >= v[i]) memo[i][j] = max(memo[i][j], memo[i-1][j-v[i]] + w[i]);
    
    return memo[i][j];
}
```



#### DP - 自底向上

```c++
dp[0][0] = 0;
for (int i = 1; i <= n; i++) {
    for (int j = 0; j <= V; j++) {
        dp[i][j] = dp[i-1][j];
        if (j >= v[i]) dp[i][j] = max(dp[i][j], dp[i-1][j-v[i]] + w[i]);
    }
}
```

* 滚动数组把所有阶段都 `& 1`就行



## 完全背包

```
dp(i, j) 表示选前i件物品到背包容量为j的最大价值
```

```
dp(i, j) = max(dp(i-1, j-k*v[i])+k*w[i]), k∈[0, j/v[i]]
```

* 转移方程解释：选前i件物体的背包容量为j的最大价值由k个状态转移过来：选了k个i物品，只要背包能装得下`j - k*v[i]>= 0`装，所有选法的最大值
* `dp(0, 0) = 0`选前0件物品到容量为j的最大价值为0

#### 暴力

```c++
// 选择前i个物品到背包容量为j的最大价值
int dfs(int i, int j) {
    if (i == 0) return 0;
    
    int res = 0;
    for (int k = 0; k * v[i] <= j; j++) {
        res = max(res, dfs(i - 1, j - k * v[i]) + k * w[i]);
    }
    
    return res;
}
```

#### 记忆化搜索

```c++
// 选择前i个物品到背包容量为j的最大价值
int dfs(int i, int j, vector<vector<int>>& memo) {
    if (i == 0) return 0;
    
    memo[i][j] = 0;
    for (int k = 0; k * v[i] <= j; k++) {
        if (memo[i-1][j-k*v[i]] == -1) memo[i-1][j-k*v[i]] = dfs(i - 1, j - k * v[i], memo);
        memo[i][j] = max(memo[i][j], memo[i-1][j-k*v[i]] + k*w[i]);
    }
    
    return memo[i][j];
}
```

#### DP - 自底向上

```c++
for (int i = 1; i <= n; i++) {
    for (int j = 0; j <= V; j++) {
        for (int k = 0; k * v[i] <= j; k++) {
            dp[i][j] = max(dp[i][j], dp[i-1][j-k*v[i]] + k*w[i]);
        }
    }
}
```



### 状态优化技巧

* 状态转移化简，为方便些公式 `v[i], w[i]` 简写成`v, w`

```
① dp(i, j) = max(dp(i-1, j),dp(i-1, j-v)+w, dp(i-1,j-2v)+2w, ..., dp(i-1,j-kv)+kw)
```

```
② dp(i,j-v) = max(          dp(i-1, j-v),   dp(i-1,j-2v)+w, ...,  dp(i-1,j-kv)+(k-1)w)
```

**联立①②：**
```
dp(i, j) = max( dp(i-1,j), dp(i,j-v)+w )
```
PS: 跟0-1背包状态**dp(i, j) = max(dp(i-1, j), dp(`i-1`, j-v[i]) + w[i])** 只相差`i-1`部分, 更改0-1背包代码i-1变成i就行就行



## 多重背包

```
dp(i, j) 表示选前i件物品到背包容量为j的最大价值
```

```
dp(i, j) = max( dp(i-1, j-k*v[i])+k*w[i] ), k∈[0, min(s[i], j / v[i])]
```

* 转移方程解释：`s[i]`为物品的个数，选前i件物体的背包容量为j的最大价值由k个状态转移过来：选了k个i物品，只要选[0,s[i]]个当前物体`k <= s[i]`并且背包能装得下`j - k*v[i]>= 0`，所有选法的最大值
* `dp(0, 0) = 0`选前0件物品到容量为j的最大价值为0

* 比只朴素版的完全背包的k多了一个限制条件`k <= s[i]`

#### DP - 自底向上

```c++
for (int i = 1; i <= n; i++) {
    for (int j = 0; j <= V; j++) {
        for (int k = 0; k * v[i] <= j && k <= s[i]; k++) {
            dp[i][j] = max(dp[i][j], dp[i-1][j-k*v[i]] + k*w[i]);
        }
    }
}
```

### 状态优化

* 二进制优化待定



## 分组背包

```
dp(i, j) 表示选前i组物品到背包容量为j的最大价值
```

```
dp(i, j) = max(dp(i-1, j), dp(i-1, j-v(i,k)+w(i,k))
```

* 转移方程解释：v(i, k)，w(i, k)分别代表第i组第k个物品的体积和价值。选前i组物体的背包容量为j的最大价值由k个状态转移过来：不选当前第i组的物品；选第k个第i组的任意一个物品。前提背包能装下`j-v(i,k) >= 0`
* `dp(0, 0) = 0`选前0件物品到容量为j的最大价值为0

#### DP - 自底向上

```c++
// vector<vector<PII>> group;   group[i][k] 代表第i组的第k个物品的信息
for (int i = 1; i <= n; i++) {
    for (int j = 0; j <= V; j++) {
        dp[i][j] = dp[i-1][j];
        for (int k = 0; k < group[i].size(); k++) {
            int v = group[i][k].first, w = group[i][k].second;
            if (j - v < 0) continue;
            dp[i][j] = max(dp[i][j], dp[i-1][j-v] + w);
        }
    }
}
```

