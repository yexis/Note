### Title 将n个物品划分成为m个组（不允许空组）（n>=m）

##### 1 emphasis

- 物品分组
- 集合划分
- **第二类斯特林数**



##### 2 key points

满足$n \ge m$，将$n$个物品划分成$m$个组，组之间无区别，不允许空组，问存在多少种方案？

$S(i,j)=S(i-1,j-1)+j \times S(i - 1, j)$



##### 3 thought

若组与组不同，问存在多少种方案？

 $T(i, j) = j! \cdot S(i,j)$



##### 4 code

**不区分组**

```cpp
const int maxn = 1000;
ll S[maxn + 10][maxn +10];
void init2() {
    memset(S, 0, sizeof(S));
    S[0][0] = 1;
    for (int i = 1; i <= maxn; i++) {
        for (int j = 1; j <= i; j++) {
            S[i][j] = S[i - 1][j - 1] + j * S[i - 1][j];
            S[i][j] %= mod;
        }
    }
}
```

**区分组**

```cpp
const int maxn = 1000;
ll fac[maxn + 10];
ll S[maxn + 10][maxn +10];
void init2() {
    memset(fac, 0, sizeof(fac));
    fac[0] = 1;
    for (int i = 1; i <= maxn; i++) {
        fac[i] = fac[i - 1] * i % mod;
    }

    memset(S, 0, sizeof(S));
    S[0][0] = 1;
    for (int i = 1; i <= maxn; i++) {
        for (int j = 1; j <= i; j++) {
            S[i][j] = S[i - 1][j - 1] + j * S[i - 1][j];
            S[i][j] %= mod;
            S[i][j] *= fac[j];
            S[i][i] %= mod;
        }
    }
}
```





##### 5 summary

###### 5.1满足$n \ge m$，将$n$个物品划分成$m$个组，组之间无区别，**若允许空组**，问存在多少种方案？

枚举将n个物品分成的组数，$i$ 从$1$枚举到$m$，然后计算$S(n,i)$

**允许空集 + 不区分组**

```cpp
const int maxn = 1000;
ll S[maxn + 10][maxn +10];
void init2() {
    memset(S, 0, sizeof(S));
    S[0][0] = 1;
    for (int i = 1; i <= maxn; i++) {
        for (int j = 1; j <= i; j++) {
            S[i][j] = S[i - 1][j - 1] + j * S[i - 1][j];
            S[i][j] %= mod;
        }
    }
}
// 注意：m <= n
ll cal(int n, int m) {
    ll ans = 0;
    for (int i = 1; i <= m; i++) {
        ans += S(n, i);
        ans %= mod;
    }
    return ans;
}
```

