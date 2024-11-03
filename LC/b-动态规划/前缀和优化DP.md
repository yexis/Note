> [3333. 找到初始输入字符串 II](https://leetcode.cn/problems/find-the-original-typed-string-ii/)

```cpp
class Solution {
public:
    using ll = long long;
    const int mod = 1e9 + 7;
    int possibleStringCount(string s, int K) {
        int n = s.size();
        vector<int> a;
        for (int i = 0; i < n; i++) {
            int j = i;
            while (j < n && s[i] == s[j]) {
                j++;
            }
            a.push_back(j - i);
            i = j - 1;
        }

        ll ans = 1;
        for (auto& e : a) {
            ans *= e;
            ans %= mod;
        }
        int m = a.size();
        if (m >= K) {
            return ans;
        }

        // m < k
        // f[i][j]表示前i个段中留下总长是j的方案数
        // f[i][j] = sum(f[i - 1][j - t]) 枚举第i段留下的总长
        ll f[m + 1][K], g[m + 1][K];
        memset(f, 0, sizeof(f));
        memset(g, 0, sizeof(g));
        f[0][0] = 1;
        for (int i = 0; i <= K - 1; i++) {
            g[0][i] = 1;
        }

        for (int i = 0; i < m; i++) {
            // 枚举前i段留下的总长度
            for (int j = 1; j <= K - 1; j++) {
                // 当前段能留下的长度范围是[1, ai]
                // 那么前i-1段留下的范围则是 [j - ai, j - 1]
                f[i + 1][j] += (g[i][j - 1] - (j - a[i] - 1 >= 0 ? g[i][j - a[i] - 1] : 0));
                f[i + 1][j] %= mod;
                g[i + 1][j] = g[i + 1][j - 1] + f[i + 1][j];
                g[i + 1][j] %= mod;

                // 枚举当前段留下的长度 [1, ai]
                // for (int t = 1; t <= a[i]; t++) {
                //     if (t > j) continue;
                //     f[i + 1][j] += f[i][j - t];
                //     f[i + 1][j] %= mod;
                // }
            }
        }

        ll diff = 0;
        for (int i = 0; i <= K - 1; i++) {
            diff += f[m][i];
            diff %= mod;
        }

        return (ans + mod - diff) % mod;
    }
};
```

