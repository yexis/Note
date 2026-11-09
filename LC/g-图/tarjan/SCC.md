### Title SCC缩点

##### 1 emphasis

- scc
- 缩点
- 强连通分量



##### 2 key points

**有向图**



**强连通**：若一张有向图的节点两两互相可达，则称这张图是强连通的。

**强连通分量（Strongly Connected Components）**：极大的强连通子图。

**对图深搜时，每一个节点只访问一次，被访问过的节点与边构成搜索树**



如果节点`x`是某个强连通分量在搜索树中遇到的第一个节点，那么这个强连通分量的其余节点肯定是在搜索树中以`x`为根的子树中。**节点`x`被称为这个强连通分量的根**



性质：

在SCC缩点完成后，scc连通分量ID越大的点，越位于有向图偏前方



##### 3 thought



##### 4 code

- https://www.luogu.com.cn/problem/P3387

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
 * SCC缩点
 * 性质：
 * 1. 相当于每个强连通分量有一个根，即每个强连通分量中dfn最小的节点，即scc[i] = low[i]的点
 * 2. 用新点构建拓扑图，scc的团号是拓扑逆序的，即团号越大的点，越靠前（因为scc时是逆序的）
 * 
 * https://www.luogu.com.cn/problem/P2812
*/

struct TarjanSCC {
    int n;
    // 时间戳编号，强连通分量个数
    // 编号都从1开始
    int tot, cnt;

    // 原图，缩点后的scc图
    vector<vector<int>> g, ng;
    
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
        for (auto& y : g[x]) {
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
};


void solve() {
    int n; cin >> n;
    TarjanSCC tj(n);

    for (int i = 0; i < n; i++) {
        int j;
        while (cin >> j, j) {
            j--;
            tj.g[i].push_back(j);
        }
    }

    auto& dfn = tj.dfn;
    auto& low = tj.low;
    auto& scc = tj.scc;
    auto& deg_in = tj.deg_in;
    auto& deg_out = tj.deg_out;

    for (int i = 0; i < n; i++) {
        if (tj.dfn[i] == -1) tj.tarjan(i);
    }

    for (int x = 0; x < n; x++) {
        for (int y : tj.g[x]) {
            if (scc[x] != scc[y]) {
                deg_in[scc[y]]++;
                deg_out[scc[x]]++;
            }
        }
    }

    // 根据题目要求而定
    // for (int x = 0; x < n; x++) {
    //     tj.nw[tj.scc[x]] += tj.w[x];
    //     for (int y : tj.g[x]) {
    //         int nx = tj.scc[x], ny = tj.scc[y];
    //         if (nx != ny) tj.ng[nx].push_back(ny);
    //     }
    // }

    int a = 0, b = 0;
    for (int i = 0; i < tj.cnt; i++) {
        if (!deg_in[i]) a++;
        if (!deg_out[i]) b++;
    }
    cout << a << "\n";
    if (tj.cnt == 1) cout << 0 << "\n";
    else cout << max(a, b) << "\n";
    return;
}

int main() {
    ios;
    cout << fixed << setprecision(20);

    int T = 1; 
    // cin >> T;
    while (T--) {
    	solve();
    }
    return 0;
}










```





##### 5 summary