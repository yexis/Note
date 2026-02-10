### Title 换根DP

##### 1 emphasis

- 换根DP

  



##### 2 key points

 

##### 3 thought



##### 4 code

[牛客小白月赛127](https://ac.nowcoder.com/acm/contest/126634/E)

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
const int mod = 998244353;
const string YES = "YES";
const string NO = "NO";

void mod_add(ll& x, ll y) { x += (mod + y); x %= mod; }

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
 * 换根DP + 深度区分
*/

void solve() {
    int n; cin >> n;
    vector<int> g[n];
    for (int i = 0; i < n - 1; i++) {
        int u, v; cin >> u >> v; u--, v--;
        g[u].push_back(v);
        g[v].push_back(u);
    }

    vector<int> dep(n);
    vector<int> cnt(n);
    vector<ll> sum(n);
    auto dfs = [&](auto&& dfs, int u, int o) -> void {
        for (auto& v : g[u]) if (v != o) {
            dep[v] = dep[u] + 1; // 计算深度 一定要放在递归前面
            dfs(dfs, v, u);
            cnt[u] += cnt[v];
            mod_add(sum[u], sum[v] + cnt[v]);
        }
        cnt[u] += 1; // 自身
    };
    dfs(dfs, 0, -1);
    

    ll ans = 0;
    // 换根DP的第二种写法，分为 子树内部 和 子树外部 两个来源
    // 子树外部：子树u外部的所有节点到节点u的结果计算
    // 子树内部：子节点到节点u的结果计算
    auto dfs2 = [&](auto&& dfs2, int u, int o, ll s, int c) -> void {
        ans += (cnt[u] - 1) + s + dep[u]; ans %= mod;
        for (auto& v : g[u]) if (v != o) {
            int cc = c + (cnt[u] - cnt[v] - 1);
            ll ss = 0; mod_add(ss, s + sum[u] - (sum[v] + cnt[v]) + cc);
            dfs2(dfs2, v, u, ss, cc);
        }
    };
    dfs2(dfs2, 0, -1, 0, 0);
    ans *= power(2, mod - 2); ans %= mod;
    cout << ans << "\n";
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





##### 5 summary

