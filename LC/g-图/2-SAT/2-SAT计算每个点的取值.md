### Title 2-SAT计算每个点的取值

##### 1 emphasis

- 2-SAT
- SCC
- 强连通分量



##### 2 examples



##### 3 key points

 

##### 4 thought

###### 4.1 2-SAT中如何判断两种选择是否存在依赖关系

1. 先从$i$出发，对图进行BFS，看能否遍历到$i + n$，结果记作$f_1$

2. 然后从$i + n$出发，对图进行BFS，看能否遍历到$i$，结果记作$f_2$

分情况讨论 ：

- 如果$f1=true, f_2=true$，显然不可能，此时两个点相互依赖，形成矛盾
- 如果$f_1=true, f_2=false$，说明$i$出发能访问到$i+n$，那么应该取$i + n$
- 如果$f_1=false, f_2=true$，说明$i+n$出发能访问到$i$，那么应该取$i$
- 如果$f_1=false, f_2=false$，说明两者没有依赖关系，那么取$i$和$i+n$都可以-



##### 5 code

https://www.luogu.com.cn/problem/P3007

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
#define next_per next_permutation
#define call(x) (x).begin(), (x).end()
#define debug(x) cout << (#x) << " = " << (x) << endl;
#define debugout(x) cout << (#x) << " = " << (x) << endl;
#define debugerr(x) cerr << (#x) << " = " << (x) << endl;

using ll = long long;
using ull = unsigned long long;
using pii = pair<int, int>;
using pli = pair<ll, int>;
using pil = pair<int, ll>;
using pll = pair<ll, ll>;
using pbi = pair<bool, int>;
using pib = pair<int, bool>;
using pis = pair<int, string>;
using psi = pair<string, int>;
using puu = pair<ull, ull>;
using arr = array<int, 3>;
using arr3 = array<int, 3>;
using arr4 = array<int, 4>;
using arr5 = array<int, 5>;

const int dir[4][2] = {{-1, 0}, {1,  0}, {0,  -1}, {0,  1}};
const int INF = 0x3f3f3f3f;
const ll LLINF = 0x3f3f3f3f3f3f3f3f;
const int mod = 1000000007;
const string YES = "YES";
const string NO = "NO";

ll mod_add(ll& x, ll y) { x += (mod + y); x %= mod; return x; }

ll power(ll x, ll b, ll m = mod) {
    ll ans = 1;
    while (b) {
        if (b & 1) {
            ans *= x;
            ans %= m;
        }
        x *= x;
        x %= m;
        b >>= 1;
    }
    return ans;
}

/*
 * 2-SAT模板
 * 
 * SCC缩点完之后得到一个有向无环图，可以考虑拓扑排序
 * 
 * 
 * 
*/

struct TarjanSCC {
    int n;
    // 时间戳编号，强连通分量个数
    // 编号都从1开始
    int tot, cnt;

    // 原图
    vector<vector<pii>> g;

    // 缩点后的scc图
    vector<vector<pii>> ng;
    
    // 时间戳，追溯值
    vector<int> dfn, low; 

    // 模拟栈，是否在栈中
    vector<int> stk, in_stk;

    // 强连通分量编号，scc的大小
    vector<int> scc, siz; 

    // 记录强连通分量入度和出度
    vector<int> deg_in, deg_out;

    // 下列内容根据实际题目设置
    // 节点权值、强连通分量权值
    vector<int> w, nw, dp;

    TarjanSCC(int nn) {
        tot = cnt = 0; n = nn;
        g.resize(n); ng.resize(n);
        dfn.resize(n, -1); low.resize(n);
        in_stk.resize(n);
        scc.resize(n, -1), siz.resize(n);
        deg_in.resize(n); deg_out.resize(n);
    }
    void tarjan(int x) {
        dfn[x] = low[x] = tot++;
        stk.push_back(x); in_stk[x] = 1;
        for (auto& [y, _] : g[x]) {
            if (dfn[y] == -1) {
                tarjan(y);
                low[x] = min(low[x], low[y]);
            } else if (in_stk[y]) {
                low[x] = min(low[x], dfn[y]);
            }
        }
        // 若x是scc的根
        if (dfn[x] == low[x]) {
            int y;
            while (true) {
                y = stk.back(); stk.pop_back(); in_stk[y] = 0;
                scc[y] = cnt; ++siz[cnt];
                if (y == x) break;
            }
            cnt++;
        }
    }
    // w 表示边(u,v)的权重
    void add_edge(int u, int v, int w) {
        g[u].push_back(pii(v, w));
    }
    // w 表示边(u,v)的权重
    void add_new_edge(int u, int v, int w) {
        ng[u].push_back(pii(v, w));
        deg_out[v]++;
        deg_in[v]++;
    }
};


void solve() {
    int n, m; cin >> n >> m;
    
    TarjanSCC scc(2 * n);

    for (int i = 0; i < m; i++) {
        int u, v; char c1, c2;
        cin >> u >> c1 >> v >> c2; u--, v--;
        int v1 = (c1 == 'Y' ? 1 : 0);
        int v2 = (c2 == 'Y' ? 1 : 0);
        // u不被满足 意味着 v必须被满足
        // v不被满足 意味着 u必须被满足
        scc.add_edge(u + !v1 * n, v + v2 * n, -1);
        scc.add_edge(v + !v2 * n, u + v1 * n, -1);
    }

    for (int i = 0; i < 2 * n; i++) {
        if (scc.dfn[i] == -1) scc.tarjan(i);
    }

    for (int i = 0; i < n; i++) {
        if (scc.scc[i] == scc.scc[i + n]) {
            cout << "IMPOSSIBLE" << "\n";
            return;
        }
    }

    vector<char> res(n, '?'); 
    
    vector<int> vis(2 * n, false);
    auto dfs = [&](auto&& dfs, int x) -> void {
        for (auto& [y, _] : scc.g[x]) {
            if (vis[y]) continue;
            vis[y] = true; dfs(dfs, y);
        }
    };

    auto check = [&](int x) -> bool {
        fill(vis.begin(), vis.end(), 0);
        vis[x] = true; dfs(dfs, x);
        if (vis[x] && vis[(x < n ? x + n : x - n)]) return true;
        else return false;
    };

    for (int i = 0; i < n; i++) {
        int a = check(i);
        int b = check(i + n);
        if (a && !b) res[i] = 'Y';
        else if (!a && b) res[i] = 'N';
        else res[i] = '?';
    }
    for (char c : res) cout << c; cout << "\n";
}

int main() {
    ios;
    cout << fixed << setprecision(20);

    int T = 1; 
    cin >> T;
    while (T--) {
    	solve();
    }
    return 0;
}



```





##### 6 summary

