### T4 [3721. 最长平衡子数组 II](https://leetcode.cn/problems/longest-balanced-subarray-ii/)

##### 1 emphasis

- HH的项链

- 动态前缀和

- lazy线段树

  



##### 2 key points

 给你一个整数数组 `nums`。

如果子数组中 **不同偶数** 的数量等于 **不同奇数** 的数量，则称该 **子数组** 是 **平衡的** 。

返回 **最长** 平衡子数组的长度。

**子数组** 是数组中连续且 **非空** 的一段元素序列。



##### 3 thought

- 假设条件为**子数组中偶数的数量等于奇数的数量，则称子数组是平衡的**，那就是[525. 连续数组](https://leetcode.cn/problems/contiguous-array/)

  > 将奇数看作$1$，将偶数看作$-1$，求前缀和$sum$
  >
  > 在前缀和数组中寻找最远的相同元素的下标距离，使用hash即可

- **对于本题，考虑类似的思路，只是需要对相同的出现的元素进行去重**

- 从左往右遍历子数组的右端点，对于当前元素$a[i]$，

  - 若$a[i]$之前出现过，上一次出现的位置设为$j$，将其变成$0$，则将 $j$ 原本对前缀和数组$[j + 1,n]$撤销掉；然后添加 $i$ 对前缀和数组的贡献

  - 若$a[i]$之前未出现过，添加 $i$ 对前缀和数组的贡献

    > 这里的贡献是指：奇数看作$1$，偶数看作$-1$
    >
    > 奇数：前缀和数组$sum[i + 1, n]$ 区间$+1$
    >
    > 偶数：前缀和数组$sum[i + 1, n]$ 区间$-1$
    >
    > 撤销贡献，则相反

- 最后，在前缀和数组中寻找最远的相同元素的下标距离



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

const int dir[4][2] = {{-1, 0},
                       {1,  0},
                       {0,  -1},
                       {0,  1}};
const int INF = 0x3f3f3f3f;
const ll LLINF = 0x3f3f3f3f3f3f3f3f;
const int mod = 1e9 + 7;
const string YES = "YES";
const string NO = "NO";

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
 * 线段树 维护动态前缀和
 * 支持区间加法
 * 维护最大值、最小值
 * 线段树二分求满足第一个等于target的下标 
*/

struct Node {
    int z;
    int mx, mi;
    Node() {
        mi = 0;
        mx = 0;
        z = 0;
    }
};
Node operator+(const Node& a, const Node& b) {
    Node res;
    res.mx = max(a.mx, b.mx);
    res.mi = min(a.mi, b.mi);
    return res;
}

struct Seg {
    static constexpr int N = 400100;
    Node tr[N + 10]{};
    Seg() {

    }
    // 标记下推
    void push_down(int o) {
        if (tr[o].z) {
            tr[o * 2].mi += tr[o].z;
            tr[o * 2].mx += tr[o].z;
            tr[o * 2].z += tr[o].z;

            tr[o * 2 + 1].mi += tr[o].z;
            tr[o * 2 + 1].mx += tr[o].z;
            tr[o * 2 + 1].z += tr[o].z;
            
            tr[o].z = 0;
        }
    }
    // 上推
    void push_up(int o) {
        tr[o] = tr[o * 2] + tr[o * 2 + 1];
    }
    void build(int o, int l, int r) {
        if (l == r) {
            tr[o] = Node();
            return;
        }
        int m = (l + r) >> 1;
        build(o * 2, l, m);
        build(o * 2 + 1, m + 1, r);
        push_up(o);
    }
    // 区间加
    void add(int o, int l, int r, int L, int R, int u) {
        if (l >= L && r <= R) {
            tr[o].mi += u;
            tr[o].mx += u;
            tr[o].z += u;
            return;
        }
        int m = (l + r) >> 1;
        push_down(o);
        if (L <= m) {
            add(o * 2, l, m, L, R, u);
        }
        if (R > m) {
            add(o * 2 + 1, m + 1, r, L, R, u);
        }
        push_up(o);
    }
    Node ask(int o, int l, int r, int L, int R) {
        if (l >= L && r <= R) {
            return tr[o];
        }
        Node res;
        int m = (l + r) >> 1;
        push_down(o);
        if (L <= m) {
            res = res + ask(o * 2, l, m, L, R);
        }
        if (R > m) {
            res = res + ask(o * 2 + 1, m + 1, r, L, R);
        }
        return res;
    }
    // 查询区间[L, R]中第一个等于target的下标
    int binary_find(int o, int l, int r, int L, int R, int target) {
        if (l > R || r < l || tr[o].mi > target || tr[o].mx < target) {
            return -1;
        }
        if (l == r) {
            return l;
        }
        push_down(o);
        int m = (l + r) >> 1;
        int idx = binary_find(o * 2, l, m, L, R, target);
        if (idx < 0) {
            return binary_find(o * 2 + 1, m + 1, r, L, R, target);
        }
        return idx;
    }
};

class Solution {
public:
    int longestBalanced(vector<int>& a) {
        int n = a.size();

        Seg seg;
        seg.build(1, 0, n);
        
        // 上一个出现的位置
        unordered_map<int, int> last;
        int ans = 0, sum = 0;
        for (int i = 1; i <= n; i++) {
            int x = a[i - 1];
            int delta = (x & 1) ? 1 : -1;
            if (last.count(x)) {
                seg.add(1, 0, n, last[x], n, -delta);
                sum -= delta;
            }
            last[x] = i;
            seg.add(1, 0, n, i, n, delta);
            sum += delta;
            int pos = seg.binary_find(1, 0, n, 0, i, sum);
            ans = max(ans, i - pos);
        }
        return ans;
    }
};

```





##### 5 summary
