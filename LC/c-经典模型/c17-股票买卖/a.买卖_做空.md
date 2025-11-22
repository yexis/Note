### Title 股票买卖 + 做空

##### 1 emphasis

- 股票买卖、做空



##### 2 key points

###### 股票买卖

先买入股票，再卖出股票 （先减再加）



做空股票

先借股票，在还（先加再减）



##### 3 thought



##### 4 code

例题：[3573. 买卖股票的最佳时机 V](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-v/)

```cpp
using ll = long long;
const ll LLINF = 1e17;
class Solution {
public:
    long long maximumProfit(vector<int>& a, int K) {
        int n = a.size();

        // f[i][k][j]表示前i天进行k笔交易，当前状态为j的最大收益
        
        // 0: 不处于交易中
        // 1: 处于买卖中 （买了股票，还没卖）
        // 2: 处于做空中 （借了股票，还没还）
        ll f[n + 1][K + 1][3];
        for (int i = 0; i <= n; i++) {
            for (int k = 0; k <= K; k++) {
                for (int j = 0; j < 3; j++) f[i][k][j] = -LLINF;
            } 
        } 
        f[0][0][0] = 0;

        auto update = [&](ll& a, ll b) -> void {
            a = max(a, b);
        };

        for (int i = 1; i <= n; i++) {
            for (int k = 0; k <= K; k++) {
                // 不操作
                for (int j = 0; j < 3; j++) f[i][k][j] = f[i - 1][k][j];
                // 结束买卖
                update(f[i][k][0], f[i - 1][k][1] + a[i - 1]);
                // 结束做空
                update(f[i][k][0], f[i - 1][k][2] - a[i - 1]);
                if (k - 1 >= 0) {
                    // 新买入
                    update(f[i][k][1], f[i - 1][k - 1][0] - a[i - 1]);
                    // 新做空
                    update(f[i][k][2], f[i - 1][k - 1][0] + a[i - 1]);
                }
            }
        }
        ll ans = -LLINF;
        for (int k = 0; k <= K; k++) {
            update(ans, f[n][k][0]);
        }
        return ans;
    }
};
```



##### 5 summary

