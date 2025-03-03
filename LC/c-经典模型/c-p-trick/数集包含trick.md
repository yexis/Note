### Title 数集包含

##### 1 emphasis



##### 2 key points

###### 定义

 数集：对于任意正整数，其数集定义为其中所有数位的数字组成的集合。例如$233$的数集为$\{2,3\}$



###### 问题

对于给定的正整数$x$，找到任意一个 $y (1 \lt y \le 10^{18} )$ 满足$x \times y$ 的数集是 $x$ 的数集的非空子集，可以证明的是，这样的构造方案一定存在



##### 3 thought

本题其实非常需要trick思维

由于数据量比较大，暴搜dfs肯定是不行的

**考虑一个trick，假设`x = 5492`，那么能不能将`x`变成`54925492`，很明显`54925492`一定是`5492`的倍数**

**换一个方向思考，回想一个乘法的竖式形式，如下**

```cpp
                a0 a1 a2 a3
        *       b0 b1 b2 b3 b4
        =          
        =       c0 c1 c2 c3
             d0 d1 d2 d3
          e0 e1 e2 e3
       f0 f1 f2 f3
    g0 g1 g2 g3
```

其实只需要中间 `d0 d1 d2 d3`，`e0 e1 e2 e3`，`f0 f1 f2 f3` 都是 `0 0 0 0 `就好了，保留`c0 c1 c2 c3`和

`g0 g1 g2 g3`分别是`a0 a1 a2 a3`就可以了，所以最后就是乘以 10001就可以了



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

void solve() {
    ll x;
    cin >> x;
    string s = to_string(x);
    int m = s.size();
    cout << '1' << string(m - 1, '0') << '1' << "\n";
}

int main() {
    ios;
    cout << fixed << setprecision(20);
    int T;
    cin >> T;
    while (T--) {
        solve();
    }
    return 0;
}

```



##### 5 summary

