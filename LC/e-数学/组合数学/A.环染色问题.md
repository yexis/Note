### Title 环染色问题

##### 1 emphasis

- 环形染色问题



##### 2 examples

https://ac.nowcoder.com/acm/contest/132903/F



##### 3 key points

 用 $k$ 种颜色，给围成一圈的 $n$ 个环形区域染色，相邻区域颜色不同，一共有多少种染色方法？



##### 4 thought

###### 结论

染色方案数为： $a_n=(k−1)^n+(−1)^n(k−1)$



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
const int mod = 998244353;
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
 * https://ac.nowcoder.com/acm/contest/131539/F
*/

void solve() {
    int n; cin >> n;
    vector<int> a(n + 1); for (int i = 1; i <= n; i++) cin >> a[i];
    
    int deg[n + 1]; memset(deg, 0, sizeof(deg));
    vector<unordered_set<int>> st(n + 1);
    vector<int> g[n + 1];
    for (int i = 1; i <= n; i++) {
        int j = a[i];
        g[i].push_back(j);
        g[j].push_back(i);
        st[i].insert(j);
        deg[i]++, deg[j]++;
    }

    
    bool in_loop[n + 1]; for (int i = 1; i <= n; i++) in_loop[i] = true;
    queue<int> q; for (int i = 1; i <= n; i++) if (deg[i] == 1) {q.push(i); in_loop[i] = false;}
    while (q.size()) {
        auto u = q.front(); q.pop();
        for (auto& v : g[u]) {
            if (--deg[v] == 1) { q.push(v); in_loop[v] = false; }
        }
    }
    int in_loop_cnt = 0; for (int i = 1; i <= n; i++) if (in_loop[i]) in_loop_cnt++;

    // bfs
    bool seen[n + 1]; for (int i = 0; i <= n; i++) seen[i] = false;
    queue<int> q2;
    ll ans = 1;
    if (in_loop_cnt) {
        for (int i = 1; i <= n; i++) if (in_loop[i]) {
            if (seen[i]) continue;
            q2.push(i); seen[i] = true;                
            int cc = 1, j = a[i];
            while (j != i) {
                q2.push(j); seen[j] = true;
                j = a[j]; cc++;
            }
            ll base = power(25, cc) + power(-1, cc) * 25; base += mod; base %= mod;
            ans *= base; ans %= mod;
        }
    } else {
        q2.push(1);
        seen[1] = true;
        ans *= 26; ans %= mod;
    }

    auto bfs = [&]() -> void {
        while (q2.size()) {
            auto u = q2.front(); q2.pop();
            for (auto& v : g[u]) if (!seen[v]) {
                q2.push(v); seen[v] = true;
                ans *= 25; ans %= mod;
            }
        }        
    };
    bfs();
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

