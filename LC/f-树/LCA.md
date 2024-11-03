### Title 最近公共祖先/树上两点距离

##### 1 emphasis

- lca
- 最近公共祖先
- 树上两点距离
- 倍增



##### 2 key points

 

##### 3 thought



##### 4 code

```cpp
struct Lca {
    // n : 节点数量
    // m : n的二进制长度
    int n, m;
    vector<int> dep;
  	// g : 邻接表
    vector<vector<int>> g;
    vector<vector<int>> st;
    // 1. 初始化 + 计算倍增数组
    Lca(int nn, vector<vector<int>>& gg) {
        n = nn;
        m = 32 - __builtin_clz(n);
        g = gg;
        dep.resize(n);
        st.resize(n, vector<int>(m, -1));

        function<void(int, int)> dfs = [&](int u, int o) {
            st[u][0] = o;
            for (auto& v : g[u]) {
                if (v != o) {
                    dep[v] = dep[u] + 1;
                    dfs(v, u);
                }
            }
        };
        dfs(0, -1);

        for (int i = 1; i < m; i++) {
            for (int u = 0; u < n; u++) {
                if (st[u][i - 1] != -1) {
                    st[u][i] = st[st[u][i - 1]][i - 1];
                }
            }
        }
    }

    // 2. 根据倍增数组计算lca
    int lca(int x, int y) {
        // 将 y 置于 较深的位置
        if (dep[x] > dep[y]) {
            swap(x, y);
        }

        // 让 x 和 y 位于同一深度
        int d = dep[y] - dep[x];
        for (; d; d &= d - 1) {
            int i = __builtin_ctz(d);
            y = st[y][i];
        }
        if (y == x) {
            return x;
        }

        // 从大到小往上跳
        for (int i = m - 1; i >= 0; i--) {
            if (st[x][i] != st[y][i]) {
                x = st[x][i];
                y = st[y][i];
            }
        }

        // 最后返回x的父节点
        return st[x][0];
    }
  
    // 点x到点y的距离
    int get(int x, int y) {
        int ca = lca(x, y);
        return dep[x] + dep[y] - 2 * dep[ca];
    }
};
```



##### 5 summary

