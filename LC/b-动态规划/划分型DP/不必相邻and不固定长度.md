### Title 划分型DP 模板

##### 1 emphasis

- 划分型DP
- **区间之间不必相邻**
- **区间长度不固定，可能会有下限**



##### 2 key points

###### 问题

给定一个整数数组$nums$，需要将$nums$划分成$k$个不同的区间，每个区间的长度至少为$m$，问所有子数组的最大和为多少？（这里可以有多种问法，总之和区间相关）



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
        
        // 前缀和：方便计算区间和
        int sum[n + 1];
        sum[0] = 0;
        for (int i = 0; i < n; i++) {
            sum[i + 1] = sum[i] + a[i];
        }
        
        // dp[i][j]表示前j个元素形成i个子数组的最大和
        // 将子数组个数定义在第一维，方便计算前缀和优化
        vector dp(K + 1, vector<int>(n + 1, -INF));
        for (int i = 0; i <= n; i++) {
            dp[0][i] = 0;
        }

        // 选 或 不选 问题
        // 不选: 
        // ① dp[i][j] = dp[i][j - 1]
        // 选: 
        // dp[i][j] = max{ dp[i - 1][L] + sum[j] - sum[L]}  L = [0, i - M]
        // sum[L]在遍历到i时无法得知，所以将sum[L]和L关联在一起，移项得:
        // ② dp[i][j] = max {(dp[i - 1][L] - sum[L]) + sum[j]}  L = [0, i - M]
        // 所以只需要维护 mx = dp[i - 1][L] - sum[L] 的最大值即可
        for (int k = 1; k <= K; k++) {
            int mx = -INF;
            for (int i = M; i <= n; i++) {
                int L = i - M;
                // 不选i（跳过当前元素）
                dp[k][i] = dp[k][i - 1];
                // 选i，枚举当前区间的长度 [M, 无穷]
                mx = max(mx, dp[k - 1][L] - sum[L]);
                dp[k][i] = max(dp[k][i], mx + sum[i]);
            }
        }
        return dp[K][n];
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

