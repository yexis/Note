### Title 计算树上节点的第k层的所有孩子

##### 1 emphasis

- 树的dfs序

- 计算树上节点第k层的所有孩子

  

##### 2 key points

适用场景：

1. 对于节点$u$，快速求出求第$k$层所有孩子节点
2. 对于节点$u$，求其第$k$层所有孩子的区间询问



##### 3 thought



##### 4 code

```cpp
using ll = long long;
using pii = pair<int, int>;
struct DFS_TREE {
    int n, tot, max_dep;
    vector<vector<int>> g;
    // L: 进入dfs序， 从1开始
    // R: 离开dfs序
    vector<int> L, R;
    // L[u]->u, R[u]->u 映射
    vector<int> r_L, r_R;
    // dep: 深度从0开始
    // sum: 观察权重位于点上还是边上?
    vector<int> dep, sum;  
    // P: 记录每一层上的节点(按DFS序)
    vector<vector<int>> P;
    // V: 若要基于第k层的孩子区间询问，可转RMQ
    vector<vector<int>> V;
    DFS_TREE(int nn, vector<vector<int>>& edges) {
        n = nn, tot = 0;
        max_dep = 0;
        g.resize(n); 
        L.resize(n); R.resize(n);
        r_L.resize(n + 1);
        P.resize(n), V.resize(n);
        dep.resize(n), sum.resize(n);
        

        for (auto& e : edges) {
            int x = e[0], y = e[1];
            g[x].emplace_back(y);
            g[y].emplace_back(x);
        }

        auto dfs = [&](this auto&& dfs, int u, int o) -> void {
            L[u] = ++tot; r_L[tot] = u;
            max_dep = max(max_dep, dep[u]);
            P[dep[u]].push_back(L[u]);
            for (auto& v : g[u]) {
                if (v == o) continue;
                dep[v] = dep[u] + 1;
                // sum[v] = sum[u] + ?
                dfs(v, u);
            }
            R[u] = tot;
        };
        dfs(0, -1);
    }
    // 获取节点u第k层的所有孩子
    // 返回所有节点的 进入dfs序 (即L[u])
    // 若为区间询问，转RMQ问题
    void get_k_child(int u, int k, vector<int>& res) {
        if (dep[u] + k > max_dep) return;
        auto& p = P[dep[u] + k];
        int idx1 = lower_bound(p.begin(), p.end(), L[u]) - p.begin();
        int idx2 = upper_bound(p.begin(), p.end(), R[u]) - p.begin() - 1;
        for (int i = idx1; i <= idx2; i++) {
            res.push_back(r_L[p[i]]);
        }
        return;
    }
};

class Solution {
public:
    long long subtreeInversionSum(vector<vector<int>>& edges, vector<int>& A, int k) {
        int n = A.size();
        DFS_TREE dt(n, edges);
        
        auto& L = dt.L;
        auto& R = dt.R; 
        vector<int> res;
        dt.get_k_child(4, 0, res);
        for (auto& e : res) cout << e << " "; cout << "\n";
        return 0;
    }
};
```



##### 5 summary

