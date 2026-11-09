### Title 2-SAT计算所有方案数

##### 1 emphasis

- 2-SAT



##### 2 examples



##### 3 key points

 

##### 4 thought



##### 5 code

https://www.luogu.com.cn/problem/P3513

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
*/

int n;
// 时间戳编号，强连通分量个数
// 编号都从1开始
int tot, cnt;

// 原图
vector<vector<int>> g;

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

void init(int nn) {
    tot = cnt = 0; n = nn;
    g.resize(n); // ng.resize(n);
    dfn.resize(n, -1); low.resize(n);
    in_stk.resize(n);
    scc.resize(n, -1); siz.resize(n);
    // deg_in.resize(n); deg_out.resize(n);
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
// w 表示边(u,v)的权重
void add_edge(int u, int v, int w) {
    g[u].push_back(v);
}
// w 表示边(u,v)的权重
void add_new_edge(int u, int v, int w) {
    ng[u].push_back(pii(v, w));
    deg_out[v]++;
    deg_in[v]++;
}


// int know[5001][5001];
bitset<5001> know[5001];
int conflict[5001];
int id[5001];
int inb[5001];
int white[5001]; 
int black[5001];
int pt1;
int pt2;

void solve() {
    
    int n; cin >> n;

    // memset(know, 0, sizeof(know));
    for (int i = 0; i < n; i++) {
        know[i].reset();
    }
    memset(conflict, 0, sizeof(conflict));
    memset(id, 0, sizeof(id));
    memset(inb, 0, sizeof(inb));
    pt1 = 0, pt2 = 0;

    for(int i = 0; i < n; i++) {
        int num; cin >> num;
        while (num--) {
            int j; cin >> j; j--;
            know[i][j] = know[j][i] = 1;
        }
    }

    init(2 * n);
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (know[i][j]) add_edge(i + n, j, -1), add_edge(j + n, i, -1);
            else add_edge(i, j + n, -1), add_edge(j, i + n, -1);
        }
    }

    for (int i = 0; i < 2 * n; i++) {
        if (dfn[i] == -1) tarjan(i);
    }

    // 先求得一个方案，然后再基于这个方案，考虑交换
    for (int i = 0; i < n; i++) {
        if (scc[i] == scc[i + n]) {
            cout << "0" << "\n";
            return;
        }
        if (scc[i] < scc[i + n]) {
            // white.push_back(i);
            white[pt1++] = i;
        } else {
            // black.push_back(i);
            black[pt2++] = i;
            inb[i] = 1;
        }
    }

    // 计算矛盾点
    for (int i = 0; i < pt1; i++) {
        for (int j = 0; j < pt2; j++) {
            int x = white[i], y = black[j];
            if (know[x][y]) {
                conflict[x]++;
                id[x] = y;
            }
        }
    }
    for (int i = 0; i < pt2; i++) {
        for (int j = 0; j < pt1; j++) {
            int x = black[i], y = white[j];
            if (!know[x][y]) {
                conflict[x]++;
                id[x] = y;
            }
        }
    }

    int ans = 0;

    // 很明显，无法一次交换>=2个人，也无法直接去对面>=2个人
    // 那一次只能操作一个人
    // - 要么这个人直接去对面
    // - 要么这个人和对面某个人交换

    // 1. 可交换的情况
    // 考虑有1个矛盾点的点：如果a有1个矛盾点，设为b，此时如果b无矛盾点，则可交换
    // 考虑有0个矛盾点的点：设white中有x个，black中有y个，那么答案是需要增加 x * y（但是很明显x和y无法同时不是0），所以答案增加0

    // 2. 可直接去对面的情况
    // 考虑有1个矛盾点的点：不存在直接过去的情况
    // 考虑有0个矛盾点的点：可以直接过去对面 答案+1
    for (int i = 0; i < n; i++) {
        // i有一个矛盾点，且这个矛盾点无矛盾点，可直接交换
        if (conflict[i] == 1 && conflict[id[i]] == 0) ans++;
        if (conflict[i] == 0 && ((inb[i] && pt2 > 1) || (!inb[i] && pt1 > 1)) ) ans++;
    }
    if (pt1 > 0 && pt2 > 0) ans++;
    cout << ans << "\n";
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

