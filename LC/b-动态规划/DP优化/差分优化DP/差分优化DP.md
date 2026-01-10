### Title 差分优化DP

##### 1 emphasis



##### 2 key points

 

##### 3 thought



##### 4 code

例题：https://ac.nowcoder.com/acm/contest/119664/F

题解：https://blog.nowcoder.net/n/0be2a91db4764792b3226dabe4044544

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
 * 
*/
constexpr int max_bit = 10;
constexpr int MAX = 1 << 10;
// 结论：对于给定的y,z，所有满足 (x ^ y <)= z的 x 至多形成 log(y) 个互不重叠的连续区间
// 计算这些区间
vector<array<int, 2>> get_xor_seg(int y, int z) {
    vector<array<int, 2>> seg;
    if (z < 0) return seg;

    int p = 0; // p在遍历的过程中，需要保证 p[i:max_bit] ^ y [i:max_bit] = z[i:max_bit]
    for (int i = max_bit - 1; i >= 0; i--) {
        if (z >> i & 1) { // z的该位是1
            if (y >> i & 1) {  // y的该位是1，说明x的该位需要是0
                seg.push_back({p | (1 << i), p | (1 << (i + 1)) - 1});
            } else { // y的该位是0，说明x的该位需要是1
                seg.push_back({p, p | (1 << i) - 1});
                p |= 1 << i;
            }
        } else { // z的该位是0
            if (y >> i & 1) { // y 的该位是 1，说明x的该位需要是1
                p |= 1 << i;
            }
        }
    }
    assert((p ^ y) == z);
    seg.push_back({p, p});
    return seg;
}

void solve() {
    auto add = [&](ll& x, ll y) -> void {
        x += y + mod;
        x %= mod;
    };
    int n; cin >> n;
    vector<int> A(n + 1), L(n + 1), R(n + 1);
    for (int i = 1; i <= n; i++) cin >> A[i] >> L[i] >> R[i];

    // 考虑前i个位置，若i为奇数，表示以j结尾的方案数；若i为偶数，表示以|P[i] - P[i-1]|的方案数
    vector<ll> dp(MAX); for (int i = 0; i <= A[1]; i++) add(dp[i], 1);

    // 场景一：i为偶数时
    // dp[i][x]表示前i个位置，最后两个元素差的绝对值是x的合法方案数
    // 设上一个元素为j，当前位元素为k  考虑 |j - k| 其中 k in [0, A[i]]
    // 若 j >= A[i]， 则 |j - k| in [j - A[i], j]
    // 若 j < A[i]， 则 |j - k| in [0, j] and in [1, A[i] - j]
    
    // 场景二： i为奇数时
    // dp[i][x]表示前i个位置，最后一个元素值为x的合法方案数
    // 设上一个元素为j，当前位元素为k
    // 对满足 k ^ j <= R[i - 1]的所有dp[i][k] 加上 dp[i-1][j]
    // 对满足 k ^ j <= L[i - 1] - 1的所有dp[i][k] 减去 dp[i-1][j]

    // 对于满足 k ^ j <= R[i - 1] 的k，是至多log(R[i-1])个连续区间，也可使用差分

    for (int i = 2; i <= n; i++) {
        vector<ll> ndp(MAX);
        for (int j = 0; j < MAX; j++) {
            if (dp[j] == 0) continue;
            if (i % 2 == 0) { // 偶数
                if (j >= A[i]) {
                    add(ndp[j - A[i]], dp[j]);
                    if (j + 1 < MAX) add(ndp[j + 1], -dp[j]);
                } else if (j < A[i]) {
                    add(ndp[0], dp[j]);
                    if (j + 1 < MAX) add(ndp[j + 1], -dp[j]);
                    add(ndp[1], dp[j]);
                    if (A[i] - j + 1 < MAX) add(ndp[A[i] - j + 1], -dp[j]);
                }
            } else { // 奇数
                auto seg_l = get_xor_seg(j, L[i - 1] - 1);
                auto seg_r = get_xor_seg(j, R[i - 1]);
                // + dp[i][j]
                for (auto& [l, r] : seg_r) {
                    l = max(0, l), r = min(r, A[i]);
                    if (l > r) continue;
                    add(ndp[l], dp[j]);
                    if (r + 1 < MAX) add(ndp[r + 1] ,-dp[j]);
                }
                // - dp[i][j]
                for (auto& [l, r] : seg_l) {
                    l = max(0, l), r = min(r, A[i]);
                    if (l >  r) continue;
                    add(ndp[l], -dp[j]);
                    if (r + 1 < MAX) add(ndp[r + 1], dp[j]);
                }
            }
        }
        // 差分还原
        for (int j = 1; j < MAX; j++) {
            add(ndp[j], ndp[j - 1]);
        }
        dp.swap(ndp);
    }
    ll ans = 0;
    for (int i = 0; i < MAX; i++) add(ans, dp[i]);
    cout << ans << "\n";
}

int main() {
    ios;
    cout << fixed << setprecision(20);

    int T = 1; 
    cin >> T;
    while (T--) {
    	solve();
    }
    return 0;
}










```



##### 5 summary

