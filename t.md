题目：

小红定义一个字符串是好串，当且仅当该字符串任意一对相邻字符都不同。

现在小红拿到了一个字符串，初始所有字符都是'0'，她希望你能维持以下操作：
· 输入1 l r1 *l* *r*，将第l*l*个字符到第r*r*个字符修改为'1'。
· 输入2 l r2 *l* *r* ，将第l*l*个字符到第r*r*个字符取反（即'1'变'0'，'0'变'1'）。
· 输入3 l r3 *l* *r* ，查询将第l*l*个字符到第r*r*个字符对应子串修改为好串的最小修改次数（每次可以修改一个字符）。



代码：

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

