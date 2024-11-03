> 矩阵快速幂



### 矩阵快速幂 

##### 1. 模板

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

```

#####  

##### 2. 使用场景

