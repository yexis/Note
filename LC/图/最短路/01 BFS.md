##### 条件

* 单源最短路径
* 相邻节点之间的距离只有 $0$ 和 $1$

##### 定义

* 使用双端队列
* 对于源点 源点 $s$ 以及任意两个节点 $u$ 和 $v$，如果 $dist(s,u) < dist(s,v)$ ($dist(x,y)$ 表示从节点 $x$ 到节点 $y$ 的最短路长度)，那么节点 $u$ 一定会比节点节点 $v$ 先被取出队列

##### 最重要的一点

保证从队列中取出的点，它们与源点之间的距离是单调递增的 （这一点与 $dijkstra$ 算法是类似的）

* $dijkstra$ 算法使用的是 `priority_queue​`
* $0/1 bfs$ 算法使用的是 `deque`

##### 操作流程

* 从 $front$ 到 $back$ 依次取出节点 $u$ （以此来保证距离是递增的）
* 遍历节点 $u$ 的相邻节点 $v$，如果 $dist(u,v)=1$，则将节点 $v$ 加入队列尾
* 遍历节点 $u$ 的相邻节点 $v$，如果 $dist(u,v)=0$，则将节点 $v$ 加入到队首

##### 代码

```cpp
		vector<int> bfs(vector<vector<pair<int, int>>>& g, int s) {
        int n = grid.size();
        vector<int> dis(n);
        deque<int> q;
        dis[s] = 0;
        q.emplace(s);
        while (!q.empty()) {
            auto u = q.front();
            q.pop_front();
            for (auto [v, w] : g[u]) {
                if (d + w < dis[v]) {
                    dis[v] = d + w;
                    if (w) {
                        q.emplace_back(v);
                    } else {
                        q.emplace_back(v);
                    }
                }
            }
        }
        return dis;
    }
```

