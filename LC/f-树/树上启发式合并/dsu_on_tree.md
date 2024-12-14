### Title 树上启发式合并

##### 1 emphasis

- 树上启发式合并
- dsu on tree



##### 2 key points

讲解：[树上启发式合并](https://www.bilibili.com/video/BV1ifUkYVEBH?spm_id_from=333.788.videopod.episodes&vd_source=a5e205ce7c5908e561eb8023831ffff2&p=4)

 时间复杂度：$O(n log(n))$

> 证明：待更新
>
> tips：统计每个节点被合并的次数



##### 3 thought



##### 4 code

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
 * 树上启发式合并
 * 第一次dfs(预处理):
 * - 子树的节点数量 sz_u
 * - 当前节点的重儿子 hs_u
 * - 维护子树的dfs序 L_u = ++tot;
 * - 维护dfs序代表的节点 id[tot] = L_u
 * 
 * 第二次dfs():
 * - 先递归轻儿子，维护答案
 * - 再递归重儿子，维护答案以及对答案的影响
 * - 最后遍历轻儿子，维护答案
 * 如果当前遍历的节点是u，计算的是以u为根节点的子树的答案
 * 轻儿子会遍历两遍，第一遍遍历是维护子树对答案的贡献；第二遍遍历是维护以u为根的链的答案
*/

//*************树状数组部分*************
// 注意这里是权值树状数组
struct BIT {
    int n;
    vector<ll> tr;
    BIT(int nn) {
        n = nn;
        tr.resize(n + 1);
    }
    int lb(int x) {
        return x & -x;
    }
    ll ask(int x) {
        ll ans = 0;
        for (int i = x; i > 0; i -= lb(i)) {
            ans += tr[i];
        }
        return ans;
    }
    ll ask(int x, int y) {
        if (x > y) return 0;
        return ask(y) - ask(x - 1);
    }
    void add(int x, ll u) {
        for (int i = x; i <= n; i += lb(i)) {
            tr[i] += u;
        }
    }
};
//*************树状数组部分*************

//*************dsu_on_tree部分*************
const int N = 1e5 + 1000;
vector<int> g[N];
int a[N], sum[N];
// L,R：dfs序，记录以u为根的子树内的所有节点，用于在第三步时清除标记
int L[N], R[N], hs[N], sz[N], id[N], tot;
ll ans = 0;
BIT bit(4e6);
void dfs(int u, int o) {
    // 进入dfs序
    L[u] = ++tot;
    hs[u] = -1;
    sz[u] = 1;
    id[tot] = u;
    if (o == -1) sum[u] = a[u];
    else sum[u] = sum[o] + a[u];

    for (auto& v : g[u]) {
        if (v == o) continue;
        dfs(v, u);
        // 计算
        sz[u] += sz[v];
        // 计算重儿子
        if (hs[u] == -1 || sz[v] > sz[hs[u]]) {
            hs[u] = v;
        }
    }
    // 离开dfs序
    R[u] = tot;
}

// ok：当前节点是否是重儿子
void dfs2(int u, int o, bool ok) {
    // 递归轻儿子
    for (auto& v : g[u]) {
        if (v == o || v == hs[u]) continue;
        dfs2(v, u, false);
    }
    // 递归重儿子
    if (hs[u] != -1) {
        dfs2(hs[u], u, true);
    }

    // 非模板部分
    auto ask = [&](int x) {
        ans += bit.ask(9 + 2 * sum[u] - sum[x] - a[u] + 1);
    };
    auto add = [&](int x) {
        bit.add(sum[x] + 1, 1);
    };
    auto del = [&](int x) {
        bit.add(sum[x] + 1, -1);
    };
    // 非模板部分

    for (auto& v : g[u]) {
        if (v == o || v == hs[u]) continue;
        // 统计重儿子与轻儿子构成的答案
        for (int i = L[v]; i <= R[v]; i++) {
            ask(id[i]);
        }
        // 将轻儿子合并到重儿子
        for (int i = L[v]; i <= R[v]; i++) {
            add(id[i]);
        }
    }
    // 统计以u为端点，往子树内延伸找另一端
    ans += bit.ask(1, 9 + sum[u] - a[u] + 1);
    // 加入当前节点u
    add(u);

    // 如果节点u是轻儿子，删除该儿子的信息
    if (!ok) {
        for (int i = L[u]; i <= R[u]; i++) {
            del(id[i]);
        }
    }
} 

void solve() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i++) cin >> a[i];
    for (int i = 0; i < n - 1; i++) {
        int u, v;
        cin >> u >> v;
        u--, v--;
        g[u].emplace_back(v);
        g[v].emplace_back(u);
    }

    dfs(0, -1);
    dfs2(0, -1, false);
    cout << ans << "\n";
}

int main() {
    solve();
    return 0;
}





```



##### 5 summary

