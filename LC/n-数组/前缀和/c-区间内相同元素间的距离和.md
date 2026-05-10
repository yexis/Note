### Title 区间内相同元素间的距离和

##### 1 emphasis

- 前缀和
- 相同元素间的距离和
- 区间内相同元素间的距离和



##### 2 examples



##### 3 key points

 

##### 4 thought

要快速计算区间内所有相同元素之间的距离和，需要设置3个前缀和数组

> 适用于区间内的元素数量不多的场景，因为需要分元素单独考虑；如区间仅由小写字母组成



以 计算区间$[l,r]$内所有`a`之间的距离和 为例

$cnt[i]$ 表示前$i$个元素中所有`a`出现的个数，更新公式为`cnt[i + 1] = cnt[i] + (s[i] - 'a' == d);`

$sum_1[i]$ 表示前$i$个元素中所有`a`到起点（下标0）的距离和，更新公式为`sum1[i + 1] = sum1[i] + i;`

$sum_2[i]$ 表示前$i$个元素中所有`a`之间的距离和，更新公式为`sum2[i + 1] = sum2[i] + ((ll)i * cnt[i]) - sum1[i];`



那么，区间$[l,r]$内所有`a`之间的距离和公式为：
$$
令 \ cnt_1 = cnt[l] \\ 
令 \ cnt_2 = cnt[r + 1] - cnt[l] \\

ans = sum_2[r + 1] - sum_2[l] - (sum_1[r + 1] - sum_1[l]) * cnt_1 - sum_1[l] * cnt_2
$$


##### 5 code

例题：https://ac.nowcoder.com/acm/contest/133523/F

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
 * 采用前缀和求解 
 * 求区间[l, r]中长度为3的回文子序列
 * 其实就是求解区间[l, r]中（任意两个相同元素之间的距离-1）之和
*/

void solve() {
    int n, q; cin >> n >> q;
    string s; cin >> s;

    vector<vector<int> > I(26);
    for (int i = 0; i < n; i++) {
        I[s[i] - 'a'].push_back(i + 1);
    }

    // cnt[d][i]: 前i个元素中d出现的个数
    vector<int> cnt[26];
    for (int d = 0; d < 26; d++) {
        cnt[d].resize(n + 1);
        for (int i = 0; i < n; i++) {
            cnt[d][i + 1] = cnt[d][i] + (s[i] - 'a' == d);
        }
    }

    // sum1[d][i]: 前i个元素中所有d到0的距离和
    // sum2[d][i]: 前i个元素中所有d之间的距离和
    vector<ll> sum1[26], sum2[26];
    for (int d = 0; d < 26; d++) {
        sum1[d].resize(n + 1);
        sum2[d].resize(n + 1);
        for (int i = 0; i < n; i++) {
            if (s[i] - 'a' == d) {
                sum1[d][i + 1] = sum1[d][i] + i;
                sum2[d][i + 1] = sum2[d][i] + ((ll)i * cnt[d][i]) - sum1[d][i] - cnt[d][i];
            } else {
                sum1[d][i + 1] = sum1[d][i];
                sum2[d][i + 1] = sum2[d][i];
            }
        }
    }

    vector<ll> res(q);
    for (int i = 0; i < q; i++) {
        int l, r, x; cin >> l >> r >> x;
        if (x == 1) {
            for (int d = 0; d < 26; d++) {
                int k1 = lower_bound(I[d].begin(), I[d].end(), l) - I[d].begin();
                int k2 = upper_bound(I[d].begin(), I[d].end(), r) - I[d].begin();
                res[i] += (k2 - k1);
            }
        } else if (x == 2) {
            for (int d = 0; d < 26; d++) {
                int k1 = lower_bound(I[d].begin(), I[d].end(), l) - I[d].begin();
                int k2 = upper_bound(I[d].begin(), I[d].end(), r) - I[d].begin();
                int delta = k2 - k1;
                if (delta <= 0) continue;
                res[i] += (ll)delta * (delta - 1) / 2;
            }
        } else if (x == 3) {
            l--, r--;
            // 求区间[l, r]内所有d之间的距离和
            for (int d = 0; d < 26; d++) {
                ll cnt1 = cnt[d][l];
                ll cnt2 = cnt[d][r + 1] - cnt[d][l];
                ll t = sum2[d][r + 1] - sum2[d][l] - 
                    ( (sum1[d][r + 1] - sum1[d][l]) * cnt1 - sum1[d][l] * cnt2 - cnt1 * cnt2 );
                res[i] += t;
            }
        }
    }

    for (auto& e : res) {
        cout << e << "\n";
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

