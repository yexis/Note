### Title 最短路 floyd

##### 1 emphasis

- floyd
- 佛洛依德
- 最短路



##### 2 key points

- 适用于数据范围较小的情况下
- 时间复杂度:$O(n^3)$



##### 3 thought



##### 4 code

**下标从1开始**

```cpp
int val[MAXN + 1][MAXN + 1];  // 原图的邻接矩阵

int floyd(const int &n) {
    static int dis[MAXN + 1][MAXN + 1];  // 最短路矩阵
    for (int i = 1; i <= n; ++i)
        for (int j = 1; j <= n; ++j) dis[i][j] = val[i][j];  // 初始化最短路矩阵
    int ans = inf;
    for (int k = 1; k <= n; ++k) {
        for (int i = 1; i <= n; ++i)
            for (int j = 1; j <= n; ++j)
                dis[i][j] = std::min(dis[i][j], dis[i][k] + dis[k][j]);  // 正常的 floyd 更新最短路矩阵
    }
    return ans;
}
```

**下标从0开始**

```cpp
int val[MAXN + 1][MAXN + 1];  // 原图的邻接矩阵

int floyd(const int &n) {
    static int dis[MAXN + 1][MAXN + 1];  // 最短路矩阵
    for (int i = 0; i < n; ++i)
        for (int j = 0; j < n; ++j) dis[i][j] = val[i][j];  // 初始化最短路矩阵
    int ans = inf;
    for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < n; ++j)
                dis[i][j] = std::min(dis[i][j], dis[i][k] + dis[k][j]);  // 正常的 floyd 更新最短路矩阵
    }
    return ans;
}
```



##### 5 summary

