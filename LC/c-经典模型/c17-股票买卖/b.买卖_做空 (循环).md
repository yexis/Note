### Title 股票买卖 + 做空 （循环数组）

##### 1 emphasis

- 股票买卖、做空
- 循环数组



##### 2 key points





##### 3 thought



##### 4 code

例题：[3743. 循环划分的最大得分](https://leetcode.cn/problems/maximize-cyclic-partition-score/)

```cpp
class Solution {
public:
    // 对于循环数组上的 股票买卖 问题
    // 考虑在最前面有一个提前买入或者做空的状态，然后基于这个提前的状态进行递推
    // 
    using ll = long long;
    const ll INF = 1e17;
    long long maximumScore(vector<int>& a, int K) {
        int n = a.size();
        
        auto update = [&](ll& a, ll b) -> void {
            a = max(a, b);
        };
        // flag = 0 初始无股票，不循环
        // flag = 1 初始有1手股票（提前买入）
        // flag = -1 初始有-1手股票（提前做空）
        auto cal = [&](int flag) -> ll {
            // y = 0 当前无股票
            // y = 1 当前处于做空中
            // y = 2 当前处于买入中
            ll f[2][K + 1][2][3]; // f[i][k][x][y]表示第i天前进行k次交易，状态为y的最大得分
            for (int k = 0; k <= K; k++) for (int x = 0; x < 2; x++) for (int y = 0; y < 3; y++) f[0][k][x][y] = -INF;
            f[0][0][0][0] = 0; f[0][1][0][1] = a[0]; f[0][1][0][2] = -a[0];
            if (flag) f[0][0][1][0] = flag * a[0];
            for (int i = 1; i < n; i++) {
                for (int k = 0; k <= K; k++) for (int x = 0; x < 2; x++) for (int y = 0; y < 3; y++) f[i & 1][k][x][y] = -INF;
                if (flag) f[i & 1][0][1][0] = flag * a[i];
                for (int k = 0; k <= K; k++) {
                    for (int x = 0; x < 2; x++) {
                        // 今天不操作股票
                        for (int y = 0; y < 3; y++) update(f[i & 1][k][x][y], f[i & 1 ^ 1][k][x][y]);

                        // 今天开始新的买入
                        if (k > 0) update(f[i & 1][k][x][2], f[i & 1 ^ 1][k - 1][x][0] - a[i]);

                        // 今天买入，结束之前做空
                        update(f[i & 1][k][x][0], f[i & 1 ^ 1][k][x][1] - a[i]);

                        // 今天卖出，开始新的做空
                        if (k > 0) update(f[i & 1][k][x][1], f[i & 1 ^ 1][k - 1][x][0] + a[i]);

                        // 今天卖出，结束之前买入
                        update(f[i & 1][k][x][0], f[i & 1 ^ 1][k][x][2] + a[i]);
                    }
                }
            }

            ll ans = -INF;
            int x, y;
            if (flag == 0) x = 0, y = 0;
            else if (flag == 1) x = 1, y = 2;
            else if (flag == -1) x = 1, y = 1;
            for (int k = 0; k <= K; k++) ans = max(ans, f[(n - 1) & 1][k][x][y]);
            return ans;
        };

        return max({cal(0), cal(1), cal(-1)});
    }
};
```



##### 5 summary

