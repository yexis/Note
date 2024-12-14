### Title 二分图最大匹配 dinic算法

##### 1 emphasis

- 二分图最大匹配
- dinic算法
- 二分图最大匹配模型转网络流最大流模型



##### 2 key points

 

##### 3 thought



##### 4 code

例题：洛谷 P3386

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

/*
 * 二分图最大匹配 - dinic算法模板
 * 二分图最大匹配 转 网络流最大流模型
 *
 * 注意：
 * 1. N(点数)和M(边数)的大小；
 *    N = 2 * (n_left + n_right) + 2;
 *    M = 2 * (n_left + n_right + n_mid)
 * 2. 时间复杂度：O(sqrt(n) * m)
 * 3. 二分图最大匹配-dinic算法中，如何查找匹配方案，没有匈牙利算法中的match数组
 *    可遍历每个点的出边，观察哪条边的容量为0，那么这条边则是匹配边之一
*/

const int N = 200010;
const int M = 300010;
struct Edge {
    int v;
    int c;
    int ne;
} e[M];
// 链式前向星
int h[N];
// 记录节点的深度
int d[N];
// 当前弧优化
int cur[N];
int idx = 1;
int n, m, k;
// 源点，汇点
int S, T;
void add(int a, int b, int c) {
    e[++idx] = {b, c, h[a]};
    h[a] = idx;
}
bool bfs() {
    memset(d, 0, sizeof(d));
    queue<int> q;
    q.push(S);
    d[S] = 1;
    while (q.size()) {
        int u = q.front();
        q.pop();
        for (int i = h[u]; i; i = e[i].ne) {
            int v= e[i].v;
            if (d[v] == 0 && e[i].c) {
                d[v] = d[u] + 1;
                q.push(v);
                if (v == T) {
                    return true;
                }
            }
        }
    }
    return false;
}

// 多路增广
int dfs(int u, int mf) {
    if (u == T) {
        return mf;
    }
    int sum = 0;
    for (int i = h[u]; i; i = e[i].ne) {
        // 当前弧优化
        cur[u] = i;
        int v = e[i].v;
        if (d[v] == d[u] + 1 && e[i].c) {
            int f = dfs(v, min(mf, e[i].c));
            // 更新残留网
            e[i].c -= f;
            e[i^1].c += f;
            sum += f;
            mf -= f;
            // 余量优化
            if (mf == 0) {
                break;
            }
        }
    }
    // 残枝优化
    if (sum == 0) d[u] = 0;
    return sum;
}

int dinic() {
    int flow = 0;
    while (bfs()) {
        memcpy(cur, h, sizeof(h));
        flow += dfs(S, 1e9);
    }
    return flow;
}

void solve() {
    cin >> n >> m >> k;

    // 1. 虚拟源点和汇点
    S = 0, T = m + n + 1;
    // 2. 建图：源点到左图
    for (int i = 1; i <= n; i++) {
        add(S, i, 1);
        add(i, S, 0);
    }
    // 3. 建图：左图到右图
    int a, b, c;
    while (k--) {
        cin >> a >> b;
        add(a, b + n, 1);
        add(b + n, a, 0);
    }
    // 4. 建图：右图到汇点
    for (int i = 1; i <= m; i++) {
        add(i + n, T, 1);
        add(T, i + n, 0);
    }
    cout << dinic() << "\n";
}

int main() {
    solve();
    return 0;
}
```





##### 5 summary

