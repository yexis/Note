### Title 划分型DP 区间不相邻&固定长度

##### 1 emphasis

- 划分型DP
- **区间之间不必相邻**
- **区间长度固定**



##### 2 key points

###### 问题

给定一个整数数组$nums$，需要将$nums$划分成$k$个不同的区间，每个区间的长度至少为$m$，问所有子数组的最大和为多少？（这里可以有多种问法，总之和区间相关）



###### 状态定义

**外层枚举区间个数$k$，内层枚举索引$i$**



###### 关键点

**不必相邻意味着可以存在 `dp[k][i] = d[k][i - 1]`的转移**，即当前元素可以不选



##### 3 thought



##### 4 code

① 定义$f[i][j]$表示前$j$个元素中划分出$i$个区间所能得到的最大和

```cpp
using ll = long long;
struct Heap {
    ll sum_l, sum_r, k;
    multiset<ll> L, R;
    Heap(int kk) {
        k = kk;
        sum_l = 0;
        sum_r = 0;
    }
    void adjust() {
        while (L.size() < k && R.size() > 0) {
            ll t = *R.begin();
            R.erase(R.begin());
            sum_r -= t;
            L.insert(t);
            sum_l += t;
        }
        while (L.size() > k) {
            ll t = *L.rbegin();
            L.erase(prev(L.end()));
            sum_l -= t;
            R.insert(t);
            sum_r += t;
        }
    }
    void add(ll x) {
        if (L.size() && x < *L.rbegin()) {
            L.insert(x);
            sum_l += x;
        } else {
            R.insert(x);
            sum_r += x;
        }
        adjust();
    }
    void del(ll x) {
        if (L.size() && x <= *L.rbegin()) {
            L.erase(L.find(x));
            sum_l -= x;
        } else {
            R.erase(R.find(x));
            sum_r -= x;
        }
        adjust();
    }

    ll ask() {
        int k1 = R.size();
        ll mid = *L.rbegin();
        return mid * k - sum_l + sum_r - mid * k1;
    }
};

class Solution {
public:
    using ll = long long;
    const ll inf = 0x3f3f3f3f3f3f3f3f;
    long long minOperations(vector<int>& a, int X, int K) {
        int n = a.size();

      	// 对顶堆实现中位数贪心
        vector<ll> v(n, 0);
        Heap ha((X + 1) / 2);
        for (int i = 0; i < n; i++) {
            ha.add(a[i]);
            if (i - X >= 0) ha.del(a[i - X]);
            if (i - X + 1 >= 0) v[i - X + 1] = ha.ask();
        }

      	// 以下是模板部分
        // dp[k][i] = max( {dp[k - 1][L] + v[L]} ) v[L]表示以L开头的区间长度为X的值
        vector<vector<ll>> dp(K + 1, vector<ll>(n + 1, inf));
        for (int i = 0; i <= n; i++) {
            dp[0][i] = 0;
        }
        for (int k = 1; k <= K; k++) {
            ll mi = inf;
            for (int i = k * X; i <= n; i++) {
                // 不选
                dp[k][i] = dp[k][i - 1];
                // 选
                int L = i - X;
                mi = min(mi, dp[k - 1][L] + v[L]);
                dp[k][i] = min(dp[k][i], mi);
            }
        }

        ll ans = inf;
        for (int i = K * X; i <= n; i++) {
            ans = min(ans, dp[K][i]);
        }
        return ans;
    }
};©leetcode
```







##### 5 summary

###### 例题

[使K个子数组内元素相等的最少操作次数](https://leetcode.cn/contest/weekly-contest-443/problems/minimum-operations-to-make-elements-within-k-subarrays-equal/description/)