### Title 线段树维护字符串轮转

##### 1 emphasis

- 线段树维护字符串轮转
- 线段树维护ABC轮转



##### 2 examples



##### 3 key points

 

##### 4 thought



##### 5 code

[小月的纸袋](https://ac.nowcoder.com/acm/contest/139206/F)

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
 * 
*/

struct Node {
    int z; // 懒加载
    int len;
    int va; // 内部极大子串A的数量
    int vb; // 内部极大子串B的数量
    int vc; // 内部极大子串C的数量
    char pre;
    char suf;
    Node() { 
        z = 0; 
        len = 0;
        va = 0;
        vb = 0;
        vc = 0; 
        pre = '?', suf = '?';
    }
    Node(char c) { 
        z = 0; 
        len = 1;
        if (c == 'A') {
            va = 1, vb = 0, vc = 0;
        } else if (c == 'B') {
            va = 0, vb = 1, vc = 0;
        } else if (c == 'C') {
            va = 0, vb = 0, vc = 1;
        }
        pre = c, suf = c;
    }
    // 轮转一次
    char shift(char c) {
        if (c == 'A') return 'B';
        else if (c == 'B') return 'C';
        else if (c == 'C') return 'A';
        return '?';
    }
    // called by push_down
    void op() {
        int tmp = vc;
        vc = vb, vb = va, va = tmp;
        pre = shift(pre);
        suf = shift(suf);
    }
};
// 自定义+
Node operator+(const Node& a, const Node& b) {
    if (a.len == 0) return b;
    if (b.len == 0) return a;

    Node res;
    res.len = a.len + b.len;
    res.va = a.va + b.va;
    res.vb = a.vb + b.vb;
    res.vc = a.vc + b.vc;
    res.pre = a.pre;
    res.suf = b.suf;
    if (a.suf == b.pre) {
        char c = a.suf;
        if (c == 'A') res.va--;
        if (c == 'B') res.vb--;
        if (c == 'C') res.vc--;
    }
    return res;
}

struct SegTree {
    string s;
    Node tr[800000];
    SegTree(string& _s) {
        s = _s;
    }
    void push_up(int o, int l, int r) {
        tr[o] = tr[o * 2] + tr[o * 2 + 1];
    }
    void push_down(int o, int l, int r) {
        if (tr[o].z) {
            tr[o * 2].op(); if (tr[o].z > 1) tr[o * 2].op();
            tr[o * 2].z += tr[o].z; tr[o * 2].z %= 3;
            tr[o * 2 + 1].op(); if (tr[o].z > 1) tr[o * 2 + 1].op();
            tr[o * 2 + 1].z += tr[o].z; tr[o * 2 + 1].z %= 3;
            tr[o].z = 0;
        }
    }
    void build(int o, int l, int r) {
        if (l == r) {
          	// init 单个元素
            tr[o] = Node(s[l - 1]);
            return;
        }
        int m = (l + r) >> 1;
        build(o * 2, l, m);
        build(o * 2 + 1, m + 1, r);
        push_up(o, l, r);
    }
    void add_lr(int o, int l, int r, int L, int R, int u) {
        if (L <= l && R >= r) {
            // upd 区间
            tr[o].z += 1; tr[o].z %= 3;
            tr[o].op();
            return;
        }
        push_down(o, l, r);
        int m = (l + r) >> 1;
        if (L <= m) {
            add_lr(o * 2, l, m, L, R, u);
        }
        if (R > m) {
            add_lr(o * 2 + 1, m + 1, r, L, R, u);
        }
        push_up(o, l, r);
    }
    Node ask(int o, int l, int r, int L, int R) {
        if (L <= l && R >= r) {
            return tr[o];
        }
        push_down(o, l, r);
        int m = (l + r) >> 1;
        Node ans;
        if (L <= m) {
            ans = ans + ask(o * 2, l, m, L, R);
        }
        if (R > m) {
            ans = ans + ask(o * 2 + 1, m + 1, r, L, R);
        }
        return ans;
    }
};

void solve() {
    int n, q; cin >> n >> q;
    string s; cin >> s;
    
    SegTree seg(s); seg.build(1, 1, n);

    while (q--) {
        int op; cin >> op;
        if (op == 1) {
            int l, r; cin >> l >> r; l-- , r--;
            seg.add_lr(1, 1, n, l + 1, r + 1, 1);
        } else if (op == 2) {
            Node ans = seg.ask(1, 1, n, 1, n);
            cout << ans.va << "\n";
        }
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

