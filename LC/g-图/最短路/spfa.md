### Title spfa (ellman-Ford算法的优化版本)

##### 1 emphasis



##### 2 key points

 

##### 3 thought



##### 4 code

```cpp
/*
 * 最短路：spfa算法，基于松弛操作的单源最短路算法，Bellman-Ford算法的优化版本
 * 每轮只访问上一轮更新过的点，因为只有上一轮被更新过的点，这一轮才有可能被更新
 * 使用队列进行维护；
 * vis[u]记录u点是否在队内；
 * cnt[v]记录边数，判负权环
 * 
*/

// pii版本
bool spfa(vector<vector<pii>>& g, int s) {
    int n = g.size();

    vector<int> d(n), vis(n), cnt(n), pre(n);
    d[s] = 0, vis[s] = true;
    queue<int> q;
    q.push(s);
    while (q.size()) {
        auto u = q.front();
        q.pop();
        vis[u] = false;
        for (auto& [v, w] : g[u]) {
            if (d[u] + w < d[v]) {
                d[v] = d[u] + w;
                pre[v] = u;
                cnt[v] = cnt[u] + 1;
                if (cnt[v] >= n) {
                    return true;
                }
                if (!vis[v]) {
                    q.push(v);
                    vis[v] = true;
                }
            }
        }
    }
    return false;
}
```



```cpp
const int N = 1e5;
struct edge {
    int v;
    int w;
};
vector<edge> g[N];
int d[N];
int cnt[N];
int vis[N];
int pre[N];
queue<int> q;
int n, m, s;
int a, b, c;

bool spfa(int s) {
    d[s] = 0;
    q.push(s);
    vis[s] = true;
    while(q.size()) {
        auto u = q.front();
        q.pop();
        vis[u] = false;
        for (auto& [v, w] : g[u]) {
            if (d[u] + w < d[v]) {
                d[v] = d[u] + w;
                pre[v] = u;
                cnt[v] = cnt[u] + 1;
                if (cnt[v] >= n) {
                    // 存在负环
                    return true;
                }
                if (!vis[v]) {
                    q.push(v);
                    vis[v] = true;
                }
            }
        }
    }
    return false;
}
void solve() {
    for (int i = 0; i < N; i++) {
        g[i].clear();
    }
    memset(d, INF, sizeof(d));
    memset(vis, 0, sizeof(vis));
    memset(cnt, 0, sizeof(cnt));
    cin >> n >> m;
    for (int i = 0; i < m; i++) {
        cin >> a >> b >> c;
        a--, b--;
        g[a].push_back(edge{b, c});
        if (c >= 0) {
            g[b].push_back(edge{a, c});
        }
    }
    cout << (spfa(0) ? YES : NO) << "\n";
}
```



##### 5 summary

