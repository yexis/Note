### Title [HH的项链](https://www.luogu.com.cn/problem/P1972)

##### 1 emphasis

- HH的项链

- 动态前缀和

  

##### 2 key points

 

##### 3 thought



##### 4 code

- 代码一：树状数组解法

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

const int dir[4][2] = {{-1, 0},
                       {1,  0},
                       {0,  -1},
                       {0,  1}};
const int INF = 0x3f3f3f3f;
const ll LLINF = 0x3f3f3f3f3f3f3f3f;
const int mod = 1e9 + 7;
const string YES = "YES";
const string NO = "NO";

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
 * 树状数组解法
 * 没遍历到一个元素x时，撤回之前出现的x的贡献，然后统计区间和
 * 单点更新，区间求和
*/

struct BIT {
    int n;
    vector<int> tr;
    BIT(int _n) {
        n = _n;
        tr.resize(n + 1);
    }
    int lb(int x) {
        return x & -x;
    }
    void add(int x, int u) {
        for (int i = x; i <= n; i += lb(i)) {
            tr[i] += u;
        }
    }
    int ask(int x) {
        if (x == 0) return 0;
        int ans = 0;
        for (int i = x; i > 0; i -= lb(i)) {
            ans += tr[i];
        }
        return ans;
    }
    int ask(int x, int y) {
        return ask(y) - ask(x - 1);
    }
};

void solve() {
    int n; cin >> n;
    vector<int> a(n);
    for (auto& e : a) cin >> e;

    int m; cin >> m;
    vector<arr> qs;
    for (int i = 0; i < m; i++) {
        int l, r;
        cin >> l >> r;
        qs.push_back({l, r, i});
    }
    sort(qs.begin(), qs.end(), [&](const auto& aa, const auto& bb) {
        if (aa[1] == bb[1]) {
            return aa[0] < bb[0];
        }
        return aa[1] < bb[1];
    });

    BIT bit(n);
    unordered_map<int, int> last;
    vector<int> res(m);
    for (int i = 1, j = 0; i <= n; i++) {
        int x = a[i - 1];
        if (last.count(x)) {
            bit.add(last[x], -1);
        }
        bit.add(i, 1);
        while (j < qs.size() && qs[j][1] == i) {
            auto [l, r, idx] = qs[j];
            res[idx] = bit.ask(l, r);
            j++;
        }
        last[x] = i;
    }
    
    for (auto& e : res) cout << e << "\n";
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

