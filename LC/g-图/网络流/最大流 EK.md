### Title  最大流 EK算法

##### 1 emphasis

- 网络最大流
- 最大流
- EK算法



##### 2 key points

- 有向图$G=(V,E)$
- 源点:$S$
- 汇点:$T$
- $f(x,y)$: 边$(x,y)$上的流量
- $c(x,y)$: 边$(x,y)$上的容量



满足条件：

- 容量限制: $f(x,y) \le c(x,y)$
- 流量守恒：进入到点$x$的的流量=从$x$点出去的流量



- 最大流：从源点流量汇点的最大流量
- 增广路：一条会源点到汇点的所有边的剩余容量$\ge 0$的路径
- 残留网：由网络中所有节点和剩余容量大于0的边构成的子图，这里的边包括有向边和其反向边，反向边的目的是提供一个“退流管道”，提供“反悔机制”



##### 3 thought



##### 4 code

> 例题：[网络最大流](https://www.luogu.com.cn/problem/P3376)

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
#define N 10010
#define M 200010
int n, m, S, T;
struct Edge {
    ll v;  // 对端
    ll c;  // 容量
    ll ne; // next
} e[M];
int h[N];
int idx = 1;
ll mf[N], pre[N];

// 链式前向星
void add(int a, int b, int c) {
    e[++idx] = {b, c, h[a]};
    h[a] = idx;
}

bool bfs() {
    memset(mf, 0, sizeof(mf));
    queue<int> q;
    q.push(S);
    mf[S] = INF;
    while (q.size()) {
        auto u = q.front();
        q.pop();
        for (int i = h[u]; i; i = e[i].ne) {
            ll v = e[i].v;
            if (mf[v] == 0 && e[i].c) {
                mf[v] = min(mf[u], e[i].c);
                pre[v] = i;
                q.push(v);
                if (v == T) {
                    return true;
                }
            }
        }
    }
    return false;
}

ll ek() {
    ll flow = 0;
    while (bfs()) {
        int v = T;
        while (v != S) {
            int i = pre[v];
            e[i].c -= mf[T];
            e[i^1].c += mf[T];
            v = e[i^1].v;
        }
        flow += mf[T];
    }
    return flow;
}

void solve() {
    int a, b, c;
    cin >> n >> m >> S >> T;
    while (m--) {
        cin >> a >> b >> c;
        add(a, b, c);
        add(b, a, 0);
    }
    cout << ek() << "\n";
}

int main() {
    solve();
    return 0;
}
```



##### 5 summary

