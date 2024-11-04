### Title [小H学生物](https://ac.nowcoder.com/acm/contest/94114/C)

##### 1 emphasis



##### 2 key points

 

##### 3 thought



##### 4 code

代码一： dfs

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
using lll = __int128;
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

void solve() {
    int n, m, L;
    cin >> n >> m >> L;
    vector<int> fa(n, -1);
    vector<vector<int> > g(n);

    // 表示节点v与其父节点之间的连边的权值
    vector<__int128> D(n);
    for (int i = 0; i < n - 1; i++) {
        int u, v;
        cin >> u >> v;
        u--, v--;
        g[u].push_back(v);
        g[v].push_back(u);
        fa[v] = u;
        string s;
        cin >> s;
        lll x = 0;
        for (int j = 0; j < L; j++) {
            if (s[j] == '1') {
                x |= ((lll)1 << j);
            }
        }
        D[v] = x;
    }
    vector<int> a(m);
    for (int i = 0; i < m; i++) {
        cin >> a[i];
        a[i]--;
    }

    // dfs: 计算根节点0到任意节点的路径异或和
    auto dfs = [&](auto&& dfs, int u, int o) -> void {
        for (auto& v : g[u]) {
            if (v == o) {
                continue;
            }
            D[v] = D[u] ^ D[v];
            dfs(dfs, v, u);
        }
    };
    dfs(dfs, 0, -1);

    lll ans = 0;
    if (m & 1) {
        ans = D[a[0]] ^ D[a[m - 1]];
    } else {
        for (int i = 1; i < m - 1; i++) {
            ans ^= D[a[i]];
        }
    }

    string ss;
    for (int i = 0; i < L; i++) {
        if (ans >> i & 1) {
            ss += '1';
        } else {
            ss += '0';
        }
    }
    cout << ss << "\n";
}

int main() {
    solve();
    return 0;
}
```



代码二：lca + 倍增

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
using lll = __int128;
using pii = pair<int, int>;
using pii128 = pair<int, __int128>;
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


struct LCA {
    // n : 节点数量
    // m : n的二进制长度
    int n, m;
    vector<int> dep;
    // g : 邻接表
    vector<int> fa;
    vector<vector<pii128> > g;
    vector<vector<int> > st;
    vector<vector<lll> > stv;
    // 1. 初始化 + 计算倍增数组
    LCA(int nn, vector<vector<pii128> >& gg, vector<int>& faa) {
        n = nn;
        m = 32 - __builtin_clz(n);
        g = gg;
        fa = faa;
        dep.resize(n);
        st.resize(n, vector<int>(m, -1));
        stv.resize(n, vector<__int128>(m, 0));

        function<void(int, int)> dfs = [&](int u, int o) {
            st[u][0] = o;
            for (auto& [v, d] : g[u]) {
                if (v != o) {
                    dep[v] = dep[u] + 1;
                    stv[v][0] = d;
                    dfs(v, u);
                }
            }
        };
        dfs(0, -1);

        for (int i = 1; i < m; i++) {
            for (int u = 0; u < n; u++) {
                if (st[u][i - 1] != -1) {
                    st[u][i] = st[st[u][i - 1]][i - 1];
                    stv[u][i] = (stv[u][i - 1] ^ stv[st[u][i - 1]][i - 1]);
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

    // 计算两个点的异或和
    lll cal(int x, int y) {
        // D_{x,y} = D_{x,lca} ^ D_{y,lca}
        int anc = lca(x, y);
        lll ans = 0;

        int d = dep[x] - dep[anc];
        for (; d; d &= d - 1) {
            int i = __builtin_ctz(d);
            ans ^= stv[x][i];
            x = st[x][i];
        }

        int d2 = dep[y] - dep[anc];
        for (; d2; d2 &= d2 - 1) {
            int i = __builtin_ctz(d2);
            ans ^= stv[y][i];
            y = st[y][i];
        }

        return ans;
    }
};

void solve() {

    int n, m, L;
    cin >> n >> m >> L;
    vector<int> fa(n, -1);
    vector<vector<pii128> > g(n);
    for (int i = 0; i < n - 1; i++) {
        int u, v;
        cin >> u >> v;
        u--, v--;

        string s;
        cin >> s;
        lll x = 0;
        for (int j = 0; j < L; j++) {
            if (s[j] == '1') {
                // 这里这里的1一定要写成 (lll)1
                x |= (((lll)1) << j);
            }
        }
        g[u].push_back({v, x});
        g[v].push_back({u, x});
        fa[v] = u;
    }
    vector<int> a(m);
    for (int i = 0; i < m; i++) {
        cin >> a[i];
        a[i]--;
    }
    
    LCA lc(n, g, fa);

    __int128 ans = 0;
    if (m & 1) {
        ans = lc.cal(a[0], a[m - 1]);
    } else {
        for (int i = 1; i + 1 < m - 1; i += 2) {
            ans ^= lc.cal(a[i], a[i + 1]);
        }
    }
    
    string ss;
    for(int i = 0; i < L; i++) {
        if ((ans >> i) & 1) {
            ss += '1';
        } else {
            ss += '0';
        }
    }
    cout << ss << "\n";
}

int main() {
    solve();
    return 0;
}
```



##### 5 summary

