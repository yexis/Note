代码一

```cpp
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
const int N = 1e6 + 10;
const int mod = 998244353;
#define endl '\n'
bool flag = false;
int cnt = 0;
#define all(x) x.begin(), x.end()
// int xx[] = { 1,0,-1,0 };
// int yy[] = { 0,1,0,-1 };
i64 qp(i64 a, i64 b)
{
    i64 res = 1;
    a %= mod;
    while (b)
    {
        if (b & 1)
            res = res * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return res;
}
#define int long long
struct node
{
    i64 v[2];
    i64 len;
    node operator+(const node &b)
    {
        node c;
        c.v[0] = v[0] + (len % 2 ? b.v[1] : b.v[0]);
        c.v[1] = v[1] + (len % 2 ? b.v[0] : b.v[1]);
        c.len = len + b.len;
        return c;
    }
    void op(int x)
    {
        v[x] = (len) / 2;
        v[x ^ 1] = len - v[x];
    }
    void work()
    {
        swap(v[0], v[1]);
    };
};
struct
{
    int l, r;
    int ls, rs;
    node c;
    i64 tag[2];
    void op(int x)
    {
        tag[0] = x;
        tag[1] = 0;
        c.op(x);
    }
    void work()
    {
        tag[1] ^= 1;
        c.work();
    }
} tr[N << 2];
int build(int l, int r)
{
    int u = ++cnt;
    tr[u].l = l;
    tr[u].r = r;
    tr[u].c.len = r - l + 1;
    tr[u].tag[0] = tr[u].tag[1] = 0;
    tr[u].op(0);
    assert(cnt < 4 * N);
    return u;
}
void push_up(int u)
{
    tr[u].c = tr[tr[u].ls].c + tr[tr[u].rs].c;
}
void push_down(int u)
{
    for (int i = 0; i < 2; i++)
        if (tr[u].tag[i])
        {
            if (i == 0)
            {
                tr[tr[u].ls].op(tr[u].tag[i]);
                tr[tr[u].rs].op(tr[u].tag[i]);
            }
            else
            {
                tr[tr[u].ls].work();
                tr[tr[u].rs].work();
            }
            tr[u].tag[i] = 0;
        }
}
void modify(int u, int l, int r, int d)
{
    if (tr[u].l >= l && tr[u].r <= r)
    {
        tr[u].op(d);
        return;
    }
    else
    {
        int mid = tr[u].l + tr[u].r >> 1;
        if (!tr[u].ls)
            tr[u].ls = build(tr[u].l, mid);
        if (!tr[u].rs)
            tr[u].rs = build(mid + 1, tr[u].r);
        push_down(u);
        if (l <= mid)
            modify(tr[u].ls, l, r, d);
        if (r > mid)
            modify(tr[u].rs, l, r, d);
        push_up(u);
    }
}
void modify2(int u, int l, int r)
{
    if (tr[u].l >= l && tr[u].r <= r)
    {
        tr[u].work();
        return;
    }
    else
    {
        int mid = tr[u].l + tr[u].r >> 1;
        if (!tr[u].ls)
            tr[u].ls = build(tr[u].l, mid);
        if (!tr[u].rs)
            tr[u].rs = build(mid + 1, tr[u].r);
        push_down(u);
        if (l <= mid)
            modify2(tr[u].ls, l, r);
        if (r > mid)
            modify2(tr[u].rs, l, r);
        push_up(u);
    }
}
node ask(int u, int l, int r)
{
    if (tr[u].l >= l && tr[u].r <= r)
        return tr[u].c;
    int mid = tr[u].l + tr[u].r >> 1;
    if (!tr[u].ls)
        tr[u].ls = build(tr[u].l, mid);
    if (!tr[u].rs)
        tr[u].rs = build(mid + 1, tr[u].r);
    push_down(u);
    if (r <= mid)
        return ask(tr[u].ls, l, r);
    if (l > mid)
        return ask(tr[u].rs, l, r);
    return ask(tr[u].ls, l, r) + ask(tr[u].rs, l, r);
}
void solve()
{
    int n, q;
    cin >> n >> q;
    int rt = build(1, n);
    for (int i = 0; i < q; i++)
    {
        int op;
        cin >> op;
        int l, r;
        cin >> l >> r;
        if (op == 1)
            modify(rt, l, r, 1);
        else if (op == 2)
            modify2(rt, l, r);
        else
        {
            node res = ask(rt, l, r);
            cout << min(res.v[0], res.v[1]) << endl;
        }
    }
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    int T = 1;
    while (T--)
        solve();
}
```





代码二

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
const ll LLINF = 0x3f3f3f3f3f3f3f3f;
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
    int tag[2];
    ll v[2];
    ll len;
    Node() : len(0) {
        v[0] = v[1] = 0;
        tag[0] = tag[1] = 0;
    }
    Node(int l) {
        len = l;
        v[0] = l / 2;
        v[1] = l - v[0];
        tag[0] = tag[1] = 0;
    }
    Node(int v0, int v1, int l) {
        v[0] = v0;
        v[1] = v1;
        len = l;
        tag[0] = tag[1] = 0;
    }
    Node operator+(const Node& b) {
        Node c;
        c.v[0] = v[0] + (len % 2 ? b.v[1] : b.v[0]);
        c.v[1] = v[1] + (len % 2 ? b.v[0] : b.v[1]);
        c.len = len + b.len;
        return c;
    }
    void op0() {
        v[0] = len / 2;
        v[1] = len - v[1];
        tag[0] = 0;
        tag[1] = 0;
    }
    // 将所有元素修改为 1
    void op1() {
        v[1] = len / 2;
        v[0] = len - v[1];
        tag[0] = 1;
        tag[1] = 0;
    }
    // 区间取反
    void op2() {
        tag[1] ^= 1;
        swap(v[0], v[1]);
    }
};

const int N = 1e4;
struct SegTree {
    int cnt;
    Node tr[N];
    int ls[N];
    int rs[N];
    SegTree() {
        cnt = 0;
    }

    void push_down(int o) {
        if (tr[o].tag[0]) {
            tr[ls[o]].op1();
            tr[rs[o]].op1();
            tr[o].tag[0] = 0;
        }
        if (tr[o].tag[1]) {
            tr[ls[o]].op2();
            tr[rs[o]].op2();
            tr[o].tag[1] = 0;
        }
    }

    void push_up(int o) {
        tr[o] = tr[ls[o]] + tr[rs[o]];
    }

    void add1(int& o, int l, int r, int L, int R, int u) {
        if (L <= l && R >= r) {
            tr[o].op1();
            return;
        }
        int m = (l + r) >> 1;
        if (!ls[o]) {
            ls[o] = ++cnt;
            tr[ls[o]] = Node(m - l + 1);
        }
        if (!rs[o]) {
            rs[o] = ++cnt;
            tr[rs[o]] = Node(r - m);
        }
        push_down(o);
        if (L <= m) {
            add1(ls[o], l, m, L, R, u);
        }
        if (R > m) {
            add1(rs[o], m + 1, r, L, R, u);
        }
        push_up(o);
    }

    void add2(int& o, int l, int r, int L, int R) {
        if (L <= l && R >= r) {
            tr[o].op2();
            return;
        }
        int m = (l + r) >> 1;
        if (!ls[o]) {
            ls[o] = ++cnt;
            tr[ls[o]] = Node(m - l + 1);
        }
        if (!rs[o]) {
            rs[o] = ++cnt;
            tr[rs[o]] = Node(r - m);
        }
        push_down(o);
        if (L <= m) {
            add2(ls[o], l, m, L, R);
        }
        if (R > m) {
            add2(rs[o], m + 1, r, L, R);
        }
        push_up(o);
    }

    Node ask(int o, int l, int r, int L, int R) {
        // if (!o) {
        //     // 未出现过，说明为全0
        //     return Node();
        // }
        if (L <= l && R >= r) {
            return tr[o];
        }
        int m = (l + r) >> 1;
        if (!ls[o]) {
            ls[o] = ++cnt;
            tr[ls[o]] = Node(m - l + 1);
        }
        if (!rs[o]) {
            rs[o] = ++cnt;
            tr[rs[o]] = Node(r - m);
        }
        push_down(o);

        Node ans;
        if (L <= m) {
            ans = ans + ask(ls[o], l, m, L, R);
        }
        if (R > m) {
            ans = ans + ask(rs[o], m + 1, r, L, R);
        }
        return ans;
    }
};

void solve() {
    int n, q;
    cin >> n >> q;

    int root = 0;
    SegTree seg;
    for (int i = 0; i < q; i++) {
        int op;
        cin >> op;
        int l, r;
        cin >> l >> r;
        if (op == 1) {
            seg.add1(root, 1, n, l, r, 1);
        } else if (op == 2) {
            seg.add2(root, 1, n, l, r);
        } else {
            Node res = seg.ask(root, 1, n, l, r);
            cout << min(res.v[0], res.v[1]) << "\n";
        }
    }
}

int main() {
    ios;
    cout << fixed << setprecision(20);
    solve();
    return 0;
}
```

帮我分析一下这两段代码功能上有什么不同，为什么第一份代码能通过题目，第二份代码却不能
