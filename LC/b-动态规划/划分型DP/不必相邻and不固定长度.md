### Title 划分型DP 区间不相邻&不固定长度

##### 1 emphasis

- 划分型DP
- **区间之间不必相邻**
- **区间长度不固定，可能会有下限**





##### 2 key points

###### 问题

给定一个整数数组$nums$，需要将$nums$划分成$k$个不同的区间，每个区间的长度至少为$m$，问所有子数组的最大和为多少？（这里可以有多种问法，总之和区间相关）



###### 状态定义

**外层枚举区间个数$k$，内层枚举索引$i$**



###### 关键点

**不必相邻意味着可以存在 `dp[k][i] = d[k][i - 1]`的转移**，即当前元素可以不选



##### 3 thought

划分型DP一版有2种遍历方式，这里称遍历方式，而不称状态定义方式，因为状态转移主要与遍历方式有关系

###### 状态定义

① 定义$f[k][i]$表示前$i$个元素中划分出$k$个区间所能得到的最大和，一般对应遍历方式①

```cpp
// 先遍历区间个数，再遍历数组
for (int k = 1; k <= K; k++) {
    for (int i = 1; i <= n; i++) {
        
    }
}
```

> **划分型DP主要以这种状态定义为主**
>
> **为什么?**
>
> **因为状态定义①  + 遍历方式①，在使用前缀和进行优化时，只需要使用一个变量mx进行维护前缀和最大值**
>
> **而 状态定义②  + 遍历方式②，在使用前缀和进行优化时，需要使用一个数组进行维护前缀和最大值**



② 定义$f[i][k]$表示前$i$个元素中划分出$k$个区间所能得到的最大和，一般对应遍历方式②

```cpp
// 先遍历数组，再遍历个数
for (int i = 1; i <= n; i++) {
    for (int k = 1; k <= K; k++) {
        
    }
}
```



###### 状态转移

对于第$i$个元素考虑 选 与 不选

不选：$f[k][i] = f[k][i-1]$

选：$f[k][i]=max_{L=(k-1)\times m}^{i-m}(f[k-1][L] + sum[i] - sum[L])$

二者取最大值，得

$f[k][i]=max \bigg( f[k][i-1], \ \  max_{L=(k-1)\times m}^{i-m}f[k-1][L] + sum[i] - sum[L] \bigg)$



##### 4 code

① 定义$f[i][j]$表示前$j$个元素中划分出$i$个区间所能得到的最大和

```cpp
// 常用模板
class Solution {
public:
    const int INF = 2e9;
    int maxSum(vector<int>& a, int K, int M) {
        int n = a.size();
        vector<int> sum(n + 1);
        for (int i = 0; i < n; i++) {
            sum[i + 1] = sum[i] + a[i];
        }

        int ans = -INF;
        // dp[i][j]：前i个元素组成j个子数组的最大和
      	// 选和不选问题
      	// ①不选，则 dp[i][j] = dp[i][j - 1]
      	// ②选，则 dp[k][i] = max( {dp[k - 1][L] + sum[i] - sum[L]} )
      	// 将sum[L]移项可得，dp[k][i] = max( {(dp[k - 1][L] - sum[L]) + sum[i]} )
        vector<vector<int>> dp(K + 1, vector<int>(n + 1, -INF));
        for (int i = 0; i <= n; i++) {
            dp[0][i] = 0;
        }
        for (int k = 1; k <= K; k++) {
            // 前缀和优化DP
            // 注意： mx只能记录与L相关的信息
            int mx = -INF;
          	// 这里可直接从 i = k * M开始，因为要划分k个区间，只要需要 k * M个元素
          	// 而 L 至少需要从 i - M 转移而来， 故 L 至少为 (k - 1) * M，即 i - M
            for (int i = k * M; i <= n; i++) {
                int L = i - M;
                // 不选
                dp[k][i] = dp[k][i - 1];
                // 选
                mx = max(mx, dp[k - 1][L] - sum[L]);
                dp[k][i] = max(dp[k][i], mx + sum[i]);
            }
        }
      
      	// 更新答案
        for (int i = K * M; i <= n; i++) {
            ans = max(ans, dp[K][i]);
        }
        return ans;
    }
};
```



② 定义$f[i][j]$表示前$i$个元素中划分出$j$个区间所能得到的最大和

```cpp
// 较复杂版本
class Solution {
public:
    const int INF = 2e9;
    int maxSum(vector<int>& a, int K, int M) {
        int n = a.size();
        vector<int> sum(n + 1);
        for (int i = 0; i < n; i++) {
            sum[i + 1] = sum[i] + a[i];
        }

        int ans = -INF;
        // dp[i][j]：前i个元素组成j个子数组的最大和
        vector<vector<int>> dp(n + 1, vector<int>(K + 1, -INF));
        dp[0][0] = 0;

        // dp2表示前缀和最大值
      	// dp2[1] 表示前i-M个元素中能划分成1个区间的最大和
      	// dp2[2] 表示前i-M个元素中能划分成2个区间的最大和
      	// ...
      	// dp2[k] 表示前i-M个元素中能划分成k个区间的最大和
        vector<int> dp2(K + 1, -INF);
        dp2[0] = 0;
        
        for (int i = 1; i <= n; i++) {
            // 前i个元素最多能划分成 i/M 个区间
            int can = i / M;
            
            // 不选i，则从i-1转移状态
            for (int k = 0; k <= min(K, can); k++) {
                dp[i][k] = dp[i - 1][k];
            }

            // 数量太少，无法划分，直接跳过
            if (i < M) continue;
            int L = i - M;

            // 更新每个划分个数的前缀最大值
            // 此时 f[L][can-1] 可用
            for (int k = 0; k <= min(K, can - 1); k++) {
                dp2[k] = max(dp2[k], dp[L][k] - sum[L]);
            }

            // 选i，则从f[L][k-1]转移过来
            // L取值于[0, i-M]，结果存储与dp2中
            for (int k = 1; k <= min(K, can); k++) {
                dp[i][k] = max(dp[i][k], dp2[k - 1] + sum[i]);
            }
        }

        return dp[n][K];
    }
};
```





##### 5 summary

###### 例题

[3473. 长度至少为 M 的 K 个子数组之和](https://leetcode.cn/problems/sum-of-k-subarrays-with-length-at-least-m/)
