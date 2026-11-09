### Title 换根DP

##### 1 emphasis

* 换根DP

    

##### 2 key points

###### 换根DP常见手段

- 求和，贡献摘除（贡献可以逆运算）
  - 除去贡献：https://ac.nowcoder.com/acm/contest/141142/G
  - 减去贡献：xxx
- 最大值与次大值
- multiset记录所有子节点状态
- 前缀和与后缀和
  - 异或

- 逆运算
- Top-K候选来源



##### 3 thought

###### 换根的步骤:

```cpp
// 保留父亲u的现场
// 保留孩子v的现场

// 父子互换
// dfs

// 恢复孩子v的现场
// 恢复父亲u的现场
```



##### 4 code

```cpp
class Solution {
public:
    vector<int> timeTaken(vector<vector<int>>& edges) {
        int m = edges.size(), n = m + 1;
        vector<int> g[n];
        for (auto& e : edges) {
            int x = e[0], y = e[1];
            g[x].push_back(y);
            g[y].push_back(x);
        }

        auto get = [&](int x, bool fg) {
            if (x == 0) {
                if (fg) {
                    return 0;
                } else {
                    return 2;
                }
            }
            if (x & 1) return 1;
            return 2;
        };

        // multiset很重要，记录每个节点的所有子节点的”数据“
        // 这样可以方便换根
        vector<multiset<int>> ms(n);
        vector<int> vv(n);
        auto dfs = [&](auto&& dfs, int u, int o) -> int {
            int sum = 0;
            for (auto& v : g[u]) {
                if (v == o) continue;
                int sumv = dfs(dfs, v, u);
                ms[u].insert(sumv);
                sum = max(sum, sumv);
            }

            vv[u] = sum + get(u, true);
            return vv[u];
        };
        dfs(dfs, 0, -1);

        vector<int> times(n);
        auto go = [&](auto&& go, int u, int o) -> void {
            times[u] = vv[u];
            for (auto& v : g[u]) {
                if (v == o) continue;
                // 换根的步骤
                // 1. 保存父亲u的数据
                int su = vv[u];
              	// 2. 保存孩子v的数据
              	int sv = vv[v];
              
                // 3. 父子互换
                // 3.1 u少了一个孩子
                ms[u].erase(ms[u].find(vv[v]));
                vv[u] = (ms[u].size() ? *ms[u].rbegin() : 0) + get(u, false);
                // 3.2 v多了一个孩子，此时v变成根节点
                ms[v].insert(vv[u]);
                vv[v] = *ms[v].rbegin();
                
                // 递归
                go(go, v, u);

                // 4. 恢复现场
                // 4.1 先恢复原孩子v，将v的新孩子u移除
                ms[v].erase(ms[v].find(vv[u]));
                // 恢复原孩子v的值
                vv[v] = sv;
                // 4.2 再恢复原父亲u，将原孩子v重新插入
                ms[u].insert(vv[v]);
                // 恢复原父亲u的值
                vv[u] = su;
            }
        };
        go(go, 0, -1);

        return times;
    }
};
```



##### 5 summary

