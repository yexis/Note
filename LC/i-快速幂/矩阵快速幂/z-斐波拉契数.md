### Title [509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/)

##### 1 emphasis

- 矩阵快速幂优化DP



##### 2 key points

 $F(0) = 0, F(1) = 1$

$F(n) = F(n - 1) + F(n - 2)，n \ge 2$



##### 3 thought

**递推公式：**$$\left[ \begin{matrix} f(i) \\ f(i - 1) \end{matrix} \right] = \left[ \begin{matrix} 1 & 1 \\ 1 & 0 \end{matrix}\right] \times \left[ \begin{matrix} f(i - 1) \\ f(i - 2) \end{matrix} \right] $$



##### 4 code

```cpp
using ll = long long;
const int mod = 1e9 + 7;
static constexpr int N = 2;
struct MA {
    vector<vector<ll>> v;
    MA() {
        v.resize(N, vector<ll>(N));
    }
    void init() {
        for (int i = 0; i < N; i++) {
            v[i][i] = 1;
        }
    }
     void all1() {
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                v[i][j] = 1;
            }
        }
    }
    MA operator* (const MA& b) {
        MA res;
        for (int i = 0 ; i < N; i++) {
            for (int j = 0; j < N; j++) {
                for (int k = 0; k < N; k++) {
                    // 注意这里是 += 
                    res.v[i][j] += v[i][k] * b.v[k][j];
                    res.v[i][j] %= mod;
                }
            }
        }
        return res;
    }

    MA operator^ (ll b) {
        MA res;
        res.init();
        MA a = *this;

        while (b) {
            if (b & 1) {
                res = res * a;
            }
            a = a * a;
            b >>= 1;
        }
        return res;
    }
};


class Solution {
public:
    int fib(int n) {
        MA M;
        M.v[0][0] = M.v[0][1] = M.v[1][0] = 1;
        
        MA B;
        B.v[0][0] = 1;

        M = M^n;
        B = M * B;
        return B.v[1][0];
    }
};
```



##### 5 summary

