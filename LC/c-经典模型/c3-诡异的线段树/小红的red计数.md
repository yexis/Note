### Title [小红的red计数](https://ac.nowcoder.com/acm/contest/91592/E)

##### 1 emphasis



##### 2 key points

 

##### 3 thought



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
 *
*/

struct Node {
    /*
     * 0: r
     * 1: re
     * 2: red
     * 3: ed
     * 4: d
     * 5: de
     * 6: der
     * 7: er
     * 8: e
     */
    ll r, re, red, ed, d, de, der, er, e;
    Node() {
        r = re = red = ed = d = de = der = er = e = 0;
    }
    const Node& operator+=(const char& ch) {
        if (ch == 'r') {
            r++;
        } else if (ch == 'e') {
            e++;
        } else if (ch == 'd') {
            d++;
        }
        return *this;
    }
};
Node operator+(const Node& a, const Node& b) {
    Node res;
    res.r = a.r + b.r;
    res.re = a.re + b.re + (a.r * b.e);
    res.red = a.red + b.red + (a.r * b.ed) + (a.re * b.d);
    res.ed = a.ed + b.ed + (a.e * b.d);
    res.d = a.d + b.d;
    res.de = a.de + b.de + (a.d * b.e);
    res.der = a.der + b.der + (a.d * b.er) + (a.de * b.r);
    res.er = a.er + b.er + (a.e * b.r);
    res.e = a.e + b.e;
    return res;
}



Node tr[400010];
void init() {

}
void add(int o, int l, int r, int i, char c) {
    if (l == r) {
        tr[o] += c;
        return;
    }
    int mid = (l + r) >> 1;
    if (i <= mid) {
        add(o * 2, l, mid, i, c);
    } else {
        add(o * 2 + 1, mid + 1, r, i, c);
    }
    // 易错点
    tr[o] = tr[o * 2] + tr[o * 2 + 1];
}

Node ask(int o, int l, int r, int L, int R) {
    if (L <= l && R >= r) {
        return tr[o];
    }

    Node ans;
    int mid = (l + r) >> 1;
    if (L <= mid) {
        ans = ans + ask(o * 2, l, mid, L, R);
    }
    // 易错点：不是else
    if (R > mid) {
        ans = ans + ask(o * 2 + 1, mid + 1, r, L, R);
    }
    return ans;
}

void solve() {
    int n, q;
    cin >> n >> q;
    string s;
    cin >> s;

    vector<pii> qs;
    for (int i = 0; i < q; i++) {
        int x, y;
        cin >> x >> y;
        x--, y--;
        qs.emplace_back(x, y);
    }

    init();
    for (int i = 0; i < n; i++) {
        add(1, 1, n, i + 1, s[i]);
    }

    auto ini = ask(1, 1, n, 1, n);
    for (auto& [l, r] : qs) {
        ll ans = ini.red;

        Node t, tt;
        t = ask(1, 1, n, l + 1, r + 1);
        ans += t.der;
        ans -= t.red;

        tt = (l > 0 ? ask(1, 1, n, 1, l) : Node());
        ans += (tt.r * t.de);
        ans -= (tt.r * t.ed);

        tt = (r + 2 <= n ? ask(1, 1, n, r + 2, n) : Node());
        ans += t.er * tt.d;
        ans -= t.re * tt.d;
        cout << ans << "\n";
    }
}

int main() {
    solve();
    return 0;
}
```



##### 5 summary

###### 5.1 手撸线段树需要注意的地方：

> 1. 询问的时候`ask(1, 1, n, 1, idx)`，其中`idx`必须满足`idx >= 1 && idx <= n`
> 2. 询问方法$ask$内部，两次判断`if (L <= mid) {}`和`if (R > mid)`是分开判断的，并非`if else关系`
> 3. 自定义Node注意重载opetor+()方法
> 4. `ask`方法的返回必须是Node类型

