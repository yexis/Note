### Title 树的 DFS 序

##### 1 emphasis

- 树的DFS序
- 后序遍历
- 左子树 → 右子树 → 父节点
- 判断节点$u$是否为节点$v$的祖先节点
- 判断节点$v$是否为节点$u$的孩子节点



##### 2 key points

 

##### 3 thought

**应用一：**

经典套路：一棵子树的 DFS 序一定是整棵树 DFS 序的连续子数组。

在部分场景下，可以根据这个结论将树上问题转换成区间问题。



**应用二:**

**快速找出节点$u$往下的第d层的所有孩子**



**应用三：**

**快速查找节点$u$往下的第d层的所有孩子的最大路径权值**

>  **假设节点$u$是节点$v$的祖先节点，那么$dfs$序满足$L_u \le  L_v \le R_v \le R_u $必成立**
>
> **对树进行分层，先找出第$dep[u] + d$层的所有节点，然后对所有节点进行二分，查询满足$L_u \le v \le R_u$的所有节点**



##### 4 code

```cpp
void solve() {
    int n;
    cin >> n;
    vector<vector<pii>> g(n);
    for (int i = 0; i < n - 1; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        u--, v--;
        g[u].emplace_back(v, w);
        g[v].emplace_back(u, w);
    }
    int tot = 0;
    int max_dep = 0;
    // 数的深度
    vector<int> dep(n);
    // 数的dfs序 L表示进入dfs序，R表示离开dfs序
    vector<int> L(n);
    vector<int> R(n);
    // 树上前缀和
    vector<ll> sum(n);

    vector<int> p[n + 1];
    vector<ll> val[n + 1];
    auto dfs = [&](auto&& dfs, int u, int o) -> void {
        if (o == -1) {
            dep[u] = 1;
        } else {
            dep[u] = dep[o] + 1;
        }
        max_dep = max(max_dep, dep[u]);
        
        L[u] = ++tot;
        p[dep[u]].push_back(L[u]);
        val[dep[u]].push_back(sum[u]);
        for (auto& [v, w] : g[u]) {
            if (v == o) continue;
            sum[v] = sum[u] + w;
            dfs(dfs, v, u);
        }
        R[u] = tot;
    };
    dfs(dfs, 0, -1);

    // rmq
    vector<RMQ> rs(max_dep + 1);
    for (int d = 1; d <= max_dep; d++) {
        rs[d].init(val[d]);
    }

    int q;
    cin >> q;
    while (q--) {
        int u, d;
        cin >> u >> d;
        u--;
        if (dep[u] + d > max_dep) {
            cout << -1 << "\n";
            continue;
        }
        d += dep[u];
        int l = L[u], r = R[u];
        int idx1 = lower_bound(p[d].begin(), p[d].end(), l) - p[d].begin();
        int idx2 = upper_bound(p[d].begin(), p[d].end(), r) - p[d].begin();
        idx2--;
        if (idx2 < idx1) {
            cout << -1 << "\n";
            continue;
        }
        cout << rs[d].ask(idx1, idx2) - sum[u] << "\n";
    }
}
```



##### 5 summary

