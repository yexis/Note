> 说明 : 在$1-10^6$范围内，因数最多的数只有240个因数



##### [2973. 树中每个节点放置的金币数目](https://leetcode.cn/problems/find-number-of-coins-to-place-in-tree-nodes/)

* **每个子树返回5个数，最小的两个数，和最大的三个数，根据这5个数，便能算法最大的3个数的乘积**

```cpp
class Solution {
public:
    vector<long long> placedCoins(vector<vector<int>> &edges, vector<int> &cost) {
        int n = cost.size();
        vector<vector<int>> g(n);
        for (auto &e: edges) {
            int x = e[0], y = e[1];
            g[x].push_back(y);
            g[y].push_back(x);
        }

        vector<long long> ans(n, 1);
        function<vector<int>(int, int)> dfs = [&](int x, int fa) -> vector<int> {
            vector<int> a = {cost[x]};
            for (int y: g[x]) {
                if (y != fa) {
                    auto res = dfs(y, x);
                    a.insert(a.end(), res.begin(), res.end());
                }
            }
            sort(a.begin(), a.end());
            int m = a.size();
            if (m >= 3) {
                ans[x] = max({(long long) a[m - 3] * a[m - 2] * a[m - 1], (long long) a[0] * a[1] * a[m - 1], 0LL});
            }
            // 根据 m > 5进行剪枝，保持始终最多返回5个数
            // 如果不够的话，有几个数返回几个数
            if (m > 5) {
                a = {a[0], a[1], a[m - 3], a[m - 2], a[m - 1]};
            }
            return a;
        };
        dfs(0, -1);
        return ans;
    }
};
```

