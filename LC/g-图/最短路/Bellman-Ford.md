### Title Bellman-Ford 单源最短路

##### 1 emphasis

- Bellman-Ford算法



##### 2 key points

Bellmn-Ford（贝尔曼-福特）算法：基于松弛操作的单源最短路算法



 适用场景：

- 可以处理负边权
- 判负环



##### 3 thought



##### 4 code

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

ll power(ll x, ll b) {
    ll ans = 1;
    while (b) {
        if (b & 1) {
            ans *= x;
            ans %= mod;
        }
        x *= x;
        x %= mod;
        b >>= 1;
    }
    return ans;
}

/*
 * 最短路：Bellman-Ford算法，基于松弛操作的单源最短路算法
 * 跑n轮，每轮检测是否存在能松弛的点；如果中间出现某轮未出现能松弛的点，提前退出
 * 如果进行n轮操作之后，仍然能进行松弛，说明存在负权环
*/

const int N = 1e5;
struct edge {
    int v;
    int w;
};
vector<edge> g[N];
int d[N];
int n, m, s;
int a, b, c;
// O(nm)
bool bellman_ford(int s) {
    d[s] = 0;
    bool fg = false;
    for (int i = 1; i <= n; i++) { // n轮 
        fg = false;
        for (int u = 0; u < n; u++) {
            if (d[u] == INF) continue;
            for (auto& [v, w] : g[u]) {
                if (d[u] + w < d[v]) {
                    d[v] = d[u] + w;
                    fg = true;
                }
            }
        }
        if (not fg) {
            break;
        }
        // 如果进行n轮操作之后，仍然能进行松弛，说明存在负权环
    }
    return fg;
}

void solve() {
    cin >> n >> m;
    for (int i = 0; i < N; i++) {
        g[i].clear();
    }
    memset(d, INF, sizeof(d));
    for (int i = 0; i < m; i++) {
        cin >> a >> b >> c;
        a--, b--;
        g[a].push_back(edge{b, c});
        if (c >= 0) {
            g[b].push_back(edge{a, c});
        }
    }
    cout << (bellman_ford(0) ? YES : NO) << "\n";
}

int main() {
    int T;
    cin >> T;
    while (T--) {
        solve();
    }
    return 0;
}
```



##### 5 summary

