### T4 [搬家](https://ac.nowcoder.com/acm/contest/90074/D) 

##### 1 emphasis



##### 2 key points

 

##### 3 thought

**模型：**

给定一个数组$a$，对于$a$中的每个下标$i$，其存在一个指针指向下标$j$，满足$j\ge i$，设下标$i$移动一步到下标$j$，问 给定一个下标$i_x$，从$i_x$出发向后移动$m$步到达哪个位置？

> 这就是典型的线性倍增的建模



##### 4 code

```cpp
#include <bits/stdc++.h>
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/hash_policy.hpp>

using namespace __gnu_pbds;
using namespace std;
#define ios ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)

template<class KT, class VT = null_type>
using RBTree = tree<KT, VT, std::less<KT>, rb_tree_tag, tree_order_statistics_node_update>;

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
 * 倍增
 * 1. 从第i个物品开始，1个收纳箱能装的物品数是固定的
 * 2. 以此类推，从第i个物品开始，m个收纳箱能装的物品数也是固定的
 *
 * 那么，如何快速计算从第i个物品开始，m个收纳箱能装的物品数？
 *  - 很明显，如果线性遍历m的话会超时；
 *  - 考虑倍增。
*/

void solve() {
    ll n, m, k;
    cin >> n >> m >> k;
    vector<ll> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }
    vector<ll> sum(n + 1);
    for (int i = 0; i < n; i++) {
        sum[i + 1] = sum[i] + a[i];
    }
    vector<int> index(n);
    iota(index.begin(), index.end(), 0);

    vector<int> next(n);
    for (int i = 0; i < n; i++) {
        int ni = lower_bound(index.begin() + i, index.end(), i, [&](const auto& aa, const auto& bb) {
            return sum[aa + 1] - sum[bb] <= k;
        }) - index.begin();
        next[i] = ni;
    }
    int Log = 32 - __builtin_clz(m);
    int st[n][Log];
    memset(st, -1, sizeof(st));
    for (int i = 0; i < n; i++) {
        st[i][0] = next[i];
    }

    for (int d = 1; d < Log; d++) {
        for (int i = 0; i < n; i++) {
            if (st[i][d - 1] == n) {
                st[i][d] = n;
            } else {
                st[i][d] = st[st[i][d - 1]][d - 1];
            }
        }
    }

    auto get = [&](int idx, int mask) {
        for (int d = Log - 1; d >= 0; d--) {
            if (mask >> d & 1) {
                idx = st[idx][d];
            }
            if (idx == n) {
                break;
            }
        }
        return idx;
    };

    int ans = 0;
    for (int i = 0; i < n; i++) {
        int ei = get(i, m);
        ans = max(ans, ei - i);
        if (ei == n) {
            break;
        }
    }
    cout << ans << "\n";
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

