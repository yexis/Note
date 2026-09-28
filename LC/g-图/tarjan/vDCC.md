### Title vDCC缩点

##### 1 emphasis

- vDCC
- tarjan
- 点双连通分量
- **点双**



##### 2 examples



##### 3 key points

**vDCC一般是针对无向图**

 **vDCC：无向图中极大的不包含割点的连通块称为”点双连通分量（Vertex Double Connected Components）**



对图深搜时，每一个节点只访问一次，被访问过的节点与边构成搜索树

时间戳`dfn[x]`：节点`x`第一次被访问的顺序

追溯值`low[x]`：从节点`x`出发，所能访问到的最早时间戳



**tarjan算法求vDCC**

- 用一个栈存点，若遍历回到`x`时，发现割点的判断法则`low[y] >= dfn[x]l`成立，则从栈中弹出节点，直到`y`被弹出
- 刚才弹出的节点和x一起构成一个vDCC （此时的`x`不一定是割点？）



**vDCC → 缩点**

**将所有点双连通分量缩为一个点，把缩点和对应的割点连边，缩完点后得到的图一定是一棵树（或森林）；然后可以观察树，构造答案**





##### 4 thought



##### 5 code

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
 * vDCC
 * 点双连通分量
 * 
 * P8435
 * https://www.luogu.com.cn/problem/P8435
 * 
*/

struct VDCC {
    int n, m, tot;

    // 连通分量的根
    // 判割点需要
    int root;

    // 原图 (u, v, id)
    int edge_id;
    vector<vector<pii>> g;

    // 缩点后的新图 (u, v, id)
    int new_edge_id;
    vector<vector<pii>> ng;

    // 时间戳，追溯值
    vector<int> dfn, low;
    
    // 是否是割点
    vector<int> cut_point;

    // 是否是割边
    vector<int> cut_edge;

    // 模拟栈
    vector<int> stk;

    // cnt: 点双连通分量编号
    // vdcc: 点双连通分量
    int cnt, cnt2;
    // vector<int> vdcc; 这里注释掉vdcc，因为割点会存在于多个点双连通分量
    vector<vector<int>> vdcc_group;
    vector<int> id;

    VDCC(int nn, int mm) {
        n = nn; m = mm; tot = 0; 
        edge_id = 0; g.resize(n); 
        // ng的大小需注意，这里先设置成2n
        new_edge_id = 0; ng.resize(2 * n);
        dfn.resize(n, -1); low.resize(n, -1);
        cut_point.resize(n), cut_edge.resize(m);
        cnt = 0; vdcc_group.resize(n);
        cnt2 = 0; id.resize(n);
    }

    void tarjan(int x, int last_id) {
        dfn[x] = low[x] = tot++; 
        stk.push_back(x);

        // 这里要注意单点自环的情况
        if (g[x].size() == 0) {
            vdcc_group[cnt++].push_back(x);
            return;
        }

        int child = 0; // 符合条件的子树个数
        for (auto [y, curr_id] : g[x]) {
            // 跳过返祖边（原代码是不跳过的，跳过应该也没问题？）
            if (curr_id == last_id) continue;

            if (dfn[y] == -1) { // 若y尚未访问
                tarjan(y, curr_id);
                // 回x时，更新low，判割点
                // 注意这里取的是low[y]
                low[x] = min(low[x], low[y]);
                if (low[y] >= dfn[x]) {
                    child++;
                    if (x != root || child > 1) {
                        cut_point[x] = true;
                    }
                    while (true) {
                        int z = stk.back(); stk.pop_back();
                        vdcc_group[cnt].push_back(z);
                        if (z == y) break;
                    }
                    vdcc_group[cnt].push_back(x);
                    cnt++;
                }
                // 判割边
                if (low[y] > dfn[x]) {
                    cut_edge[curr_id] = true;
                }
            } else { // 若y已经访问
                // 注意这里取的是dfn[y]
                low[x] = min(low[x], dfn[y]);
            }
        }
    }
    void add_edge(int u, int v) {
        g[u].push_back(pii(v, edge_id));
        g[v].push_back(pii(u, edge_id));
        edge_id++;
    }
    void add_new_edge(int u, int v) {
        ng[u].push_back(pii(v, new_edge_id));
        ng[v].push_back(pii(u, new_edge_id));
        new_edge_id++;
    }
    void set_root(int rt) {
        root = rt;
    }
};


void solve() {
    int n, m; cin >> n >> m;
    
    VDCC vdcc(n, m);
    for (int i = 0; i < m; i++) {
        int u, v; cin >> u >> v; u--, v--;
        if (u == v) continue;
        vdcc.add_edge(u, v);
    }

    for (int i = 0; i < n; i++) {
        if (vdcc.dfn[i] == -1) {
            vdcc.set_root(i);
            vdcc.tarjan(i, -1);
        }
    }
    
    vdcc.cnt2 = vdcc.cnt;
    for (int i = 0; i < n; i++) {
        if (vdcc.cut_point[i]) vdcc.id[i] = vdcc.cnt2++;
    }
    // 建新图，vdcc的图，没个缩点需要和对应割点连边
    for (int i = 0; i < vdcc.cnt; i++) {
        for (int j = 0; j < vdcc.vdcc_group[i].size(); j++) {
            int x = vdcc.vdcc_group[i][j];
            if (vdcc.cut_point[x]) {
                vdcc.add_new_edge(i, vdcc.id[x]);
            }
        }
    }
    
    cout << vdcc.cnt << "\n";
    for (int i = 0; i < vdcc.cnt; i++) {
        cout << (int)vdcc.vdcc_group[i].size() << " ";
        for (int j = 0; j < vdcc.vdcc_group[i].size(); j++) {
            int x = vdcc.vdcc_group[i][j];
            cout << x + 1 << " ";
        }
        cout << "\n";
    }
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



##### 6 summary

