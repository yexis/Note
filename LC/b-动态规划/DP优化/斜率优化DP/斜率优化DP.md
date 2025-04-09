### Title 斜率优化DP

##### 1 emphasis

- 斜率优化DP



##### 2 key points

 ###### 使用场景

若状态转移方程形如（不包含 $i$ 和 $j$ 的乘积项）：
$$
f[i] = minmax(f[j] + a[i] + b[j]), \ \ j \in [0, i - 1]
$$
则使用单调队列优化；（其中 $a[i]$ 表示与 $i$ 相关的公式，$b[j]$ 表示与 $j$ 相关的公式）



若状态转移方程形如 （包含 $i$ 和 $j$ 的乘积项）：
$$
f[i] = minmax(f[j] + a[i] * b[j]), \ \ j \in [0, i - 1]
$$
则使用斜率优化（凸包优化）；（其中 $a[i]$ 表示与 $i$ 相关的公式，$b[j]$ 表示与 $j$ 相关的公式）

> 题：对于一段连续的$n$个元素的区间数组$C$，需要将数组$C$划分成若干个子区间，每个区间的代价为$(\sum_{i=l}^rC_i)^2+M$，$M$是一个已知的常量，问划分的最小代价是多少。数据：$n\le5* 10^5,M \le 1000$
>
> $f[i]$表示前$i$个元素的最小代价（这里以最小代价举例）
>
> $s[i]$表示前缀和
>
> 转移方程 $f[i] = min(f[j] + (s_i - s_j)^2 + M), j \in [0,i-1]$
>
> 则 $f[i] = min(f[j] + s_i^2 + s_j^2 - 2s_is_j) + M $
>
> **将含 $j$ 的项看作变量，含 $i$ 的项看作常量，因为遍历到当前位置 $i$ 时，$i$相关的信息是已知的**
>
> 转换成截距式$y=kx+b$， 所以移项得，$f_j + s_j^2 = 2s_is_j + f_i - s_i^2 + M$
>
> 对应关系如下：
>
> $y = f_j + s_j^2$
>
> $k = 2s_i$
>
> $x = s_j$
>
> $b = f_i - s_i^2 + M$
>
> 当 $j$ 取不同的取值时，意味着$(x,y)$对应平面上不同的点（$x$ 和 $y$ 都可以使用 $j$ 表达）
>
> - 当 $i$ 确定时，斜率 $k$ 确定，若截距 $b$ 最小，则 $f_i$ 最小
> - 当 $i$ 增加时，斜率 $k=2s_i$ 增加，单调队列维护决策点的下凸包



###### 单调队列维护凸包模板（以下凸包为例）

![image-20250405151106470](/Users/yex/Library/Application Support/typora-user-images/image-20250405151106470.png)

很明显，下凸包最优决策点 $p$ 存在性质：位于 $p$ 左侧的线段斜率 $\lt k$， 位于 $p$ 右侧的线段斜率 $ \ge k$， 

**且随着 $i$ 的增加，斜率 $k$ 是单调递增的，所以可以使用单调队列进行维护**

单调队列维护步骤：

> 1.  更新队尾：若当前点 $i$ 与队尾点构成的线段斜率 $\le$队尾相邻点的斜率，则队尾出队，删除无用点，维护决策点的下凸包
>
> 2. 当前点入队：$i$入队，成为新队尾
>
> 3. 更新队首：若队头邻点直线的斜率 $\le k_i$ ，则队头出队，删除无用点
>    - 这里为什么要更新队首呢？因为随着 $i$ 的增加，斜率 $k_i$是单调递增的，当前队头斜率$\le k_i$ 的节点，在后续不可能再成为决策点；同时也是为了找到当前最优决策点
>
> 4. 寻找最优决策点：此时队头就是最优决策点，可进行状态转移



###### code

```cpp
```





##### 3 thought



##### 4 code

[模板：3500. 将数组分割为子数组的最小代价](https://leetcode.cn/problems/minimum-cost-to-divide-array-into-subarrays/)

```cpp
/*
f[i] = f[j] + s1[i] * s2[i] - s1[i] * s2[j] + K * s2[n] - K * s2[j]
=> f[j] - K * s2[j] = s1[i] * s2[j] + f[i] - s1[i] * s2[i] - K * s2[n]

y = kx + b; 下凸包，i作为常量，j作为变量
y = f[j] - K * s2[j]
k = s1[i]
x = s2[j]
b = f[i] - s1[i] * s2[i] - K * s2[n]
*/

// 模板部分 开始
using ll = long long;
struct Point {
    ll x, y, idx; // idx表示转移来源的下标，便于查问题
    Point operator+(const Point& that) {
        return {x + that.x, y + that.y, -1};
    }
    Point operator-(const Point& that) {
        return {x - that.x, y - that.y, -1};
    }
    // 方法一：将 y1 / x1 < y2 / x2 转换成乘法 y1 * x2 < y2 * x1
    // 说明：此处的 lhs 和 rhs 中的 x/y 代表的是 dx/dy
    // 前提：需要满足 dx >= 0
    // < 0: 说明 lhs的斜率 小于 rhs 的斜率
    // = 0: 说明 lhs的斜率 等于 rhs 的斜率
    // > 0: 说明 lhs的斜率 大于 rhs 的斜率
    friend ll cross(Point lhs, Point rhs) {
        return lhs.y * rhs.x - lhs.x * rhs.y;
    }
 	  // 方法二：直接计算斜率，不要求dx > 0，但是double会有精度问题，判断斜率时不要使用=符号，使用 > 和 <
    friend double slope(Point lhs, Point rhs) {
        return lhs.x == rhs.x ? 1e9 : (lhs.y - rhs.y) / (lhs.x - rhs.x);
    }
};
// 模板部分 结束

/*
 * 使用斜率优化需要满足斜率是单调的，单调增（下凸包） 或者 单调减（上凸包） 都可以
*/

class Solution {
public:
    long long minimumCost(vector<int>& a, vector<int>& b, int K) {
        int n = a.size();
        vector<ll> s1(n + 1); 
        vector<ll> s2(n + 1); 
        for (int i = 0; i < n; i++) {
            s1[i + 1] = s1[i] + a[i];
            s2[i + 1] = s2[i] + b[i];
        }

        ll dp[n + 1];
        memset(dp, 0, sizeof(dp));
        /*
        y = kx + b; 下凸包，i作为常量，j作为变量
        y = f[j] - K * s2[j]
        k = s1[i]
        x = s2[j]
        b = f[i] - s1[i] * s2[i] - K * s2[n]
        */

        deque<Point> q;
        for (int i = 1; i <= n; i++) {
            // 更新队尾 (x, y)
            Point lst(s2[i - 1], dp[i - 1] - K * s2[i - 1], i - 1);
            // （踩坑）这里一定要注意：需要满足队列尾部相邻节点的斜率 需要 大于 新节点和尾部节点的斜率 才需要出队，因为这样不满足下凸包
            while (q.size() >= 2 && cross(q[q.size() - 1] - q[q.size() - 2], lst - q[q.size() - 1]) >= 0) {
                q.pop_back();
            }
            q.push_back(lst);

            // 更新队首 k_i
            Point p_k = {1, s1[i], -1};
            while (q.size() >= 2 && cross(q[1] - q[0], p_k) <= 0) {
                q.pop_front();
            }
            
            // 得到答案 队首就是最优决策点
            Point& pj = q.front();
            dp[i] = cross(pj, p_k) + s1[i] * s2[i] + K * s2[n];
        }
        return dp[n];
    }
};
```



##### 5 summary

