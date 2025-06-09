### Title 树的 DFS 序

##### 1 emphasis

- dfs序
- DFS序
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

[牛客周赛67-f 小Z的树迁移](https://ac.nowcoder.com/acm/contest/95016/F)

```cpp
#include <iostream>
#include <vector>
#include <string.h>
#include <algorithm>
#include <numeric>
#include <set>
#include <array>
#include <cassert>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <iomanip>
#include <string>
#include <sstream>
#include <vector>
#include <queue>
#include <stack>
#include <list>
#include <set>
#include <map>
#include <unordered_set>
#include <unordered_map>
#include <algorithm>
#include <complex>
#include <cmath>
#include <numeric>
#include <bitset>
#include <functional>
#include <random>
#include <ctime>
#include <limits>
#include <climits>

using namespace std;
#define ios ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)


using ll = long long;
using ull = unsigned long long;
using pii = pair<int, int>;
using pli = pair<ll, int>;
using pil = pair<int, ll>;
using pll = pair<ll, ll>;
using puu = pair<ull, ull>;
const int dir[4][2] = {{-1, 0},
                       {1,  0},
                       {0,  -1},
                       {0,  1}};
const int INF = 0x3f3f3f3f;
const int mod = 1e9 + 7;
const string YES = "YES";
const string NO = "NO";

/*
 * pass
 * 树上前缀和 + dfs + 二分 + RMQ
*/

static constexpr int maxn = 100010;
struct RMQ {
    int Log;
    int n;
    vector<vector<ll>> st;
    // 采用vector<RMQ>时，可以将其改造成init方法
    void init(vector<ll>& ob) {
        n = ob.size();
        // Log 存储n以内最大的二次幂指数，实在不行可以设置成最大 32
        Log = 32 - __builtin_clz(n);
        // 这里一定不能使用resize，resize不会更新已有范围
        st.assign(n, vector<ll>(Log, -1));
        for (int i = 0; i < n; i++) {
            st[i][0] = ob[i];
        }

        for (int d = 1;  d < Log; d++) {
            for (int i = 0; i + (1 << (d - 1)) < n; i++) {
                st[i][d] = max(st[i][d - 1], st[i + (1 << (d - 1))][d - 1]);
            }
        }
    }

    ll ask(int l, int r) {
        if (l > r) {
            return -1;
        }
        int d = 31 - __builtin_clz(r - l + 1);
        return max(st[l][d], st[r - (1 << d) + 1][d]);
    }
};

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
  	// dfs序从 1 开始，[ L[u], R[u] ] 表示节点u的子树
    vector<int> L(n), R(n);
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
        // tot 从 1 开始
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
        // [idx1, idx2 - 1]为节点u的位于dep[u] + d层的左右孩子节点
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

int main() {
    solve();
    return 0;
}
```



##### 5 summary

