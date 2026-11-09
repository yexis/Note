> * st表
>
> * rmq问题



##### 零 最大最小值 MaxMin 

```cpp
static constexpr int maxn = 100010;
typedef long long K;
typedef pair<K, K> pkk;
struct RMQMAXMIN {
    int Log;
    int n;
    vector<vector<K>> st;
    vector<vector<K>> st_min;
    // 采用vector<RMQ>时，可以将其改造成init方法
    RMQMAXMIN(vector<K>& ob) {
        n = ob.size();
        // Log 存储n以内最大的二次幂指数，实在不行可以设置成最大 32
        Log = 32 - __builtin_clz(n);
        // 这里一定不能使用resize，resize不会更新已有范围
        st.assign(n, vector<K>(Log, -1));
        st_min.assign(n, vector<K>(Log, -1));
        for (int i = 0; i < n; i++) {
            st[i][0] = ob[i];
            st_min[i][0] = ob[i];
        }

        for (int d = 1;  d < Log; d++) {
            for (int i = 0; i + (1 << (d - 1)) < n; i++) {
                st[i][d] = max(st[i][d - 1], st[i + (1 << (d - 1))][d - 1]);
                st_min[i][d] = min(st_min[i][d - 1], st_min[i + (1 << (d - 1))][d - 1]);
            }
        }
    }

    pkk ask(int l, int r) {
        if (l > r) {
          	// 注意
            return {1e18, -1};
        }
        int d = 31 - __builtin_clz(r - l + 1);
        return {
            max(st[l][d], st[r - (1 << d) + 1][d]), 
            min(st_min[l][d], st_min[r - (1 << d) + 1][d]),
        };
    }
};
```



##### 一 与运算 & 

```cpp
static constexpr int maxn = 100010;
struct RMQAND {
    int Log;
    int n;
    vector<vector<int>> st;
    RMQAND(vector<int>& ob) {
        n = ob.size();
        // Log 存储n以内最大的二次幂指数，实在不行可以设置成最大 32
        Log = 32 - __builtin_clz(n);
        // 这里一定不能使用resize，resize不会更新已有范围
        st.assign(n, vector<int>(Log, -1));
        for (int i = 0; i < n; i++) {
            st[i][0] = ob[i];
        }

        for (int d = 1;  d < Log; d++) {
            for (int i = 0; i + (1 << (d - 1)) < n; i++) {
                st[i][d] = st[i][d - 1] & st[i + (1 << (d - 1))][d - 1];
            }
        }
    }

    int ask(int l, int r) {
        if (l > r) {
            return -1;
        }
        int d = 31 - __builtin_clz(r - l + 1);
        return st[l][d] & st[r - (1 << d) + 1][d];
    }
};
```



##### 二 或运算 |

```cpp
struct RMQOR {
    int Log;
    int n;
    vector<vector<int>> st;
    RMQOR(vector<int>& ob) {
        n = ob.size();
        // Log 存储n以内最大的二次幂指数，实在不行可以设置成最大 32
        Log = 32 - __builtin_clz(n);
        // 这里一定不能使用resize，resize不会更新已有范围
        st.assign(n, vector<int>(Log, -1));
        for (int i = 0; i < n; i++) {
            st[i][0] = ob[i];
        }

        for (int d = 1;  d < Log; d++) {
            for (int i = 0; i + (1 << (d - 1)) < n; i++) {
                st[i][d] = st[i][d - 1] | st[i + (1 << (d - 1))][d - 1];
            }
        }
    }

    int ask(int l, int r) {
        if (l > r) {
            return -1;
        }
        int d = 31 - __builtin_clz(r - l + 1);
        return st[l][d] | st[r - (1 << d) + 1][d];
    }
};
```



##### 三 最大值 max

```cpp
static constexpr int maxn = 100010;
struct RMQMAX {
    int Log;
    int n;
    vector<vector<ll>> st;
    // 采用vector<RMQ>时，可以将其改造成init方法
    RMQMAX(vector<ll>& ob) {
        n = ob.size();
        // Log 存储n以内最大的二次幂指数，实在不行可以设置成最大 32
        Log = 32 - __builtin_clz(n);
        // 这里一定不能使用resize，resize不会更新已有范围
        st.assign(n, vector<ll>(Log, -1));
        for (int i = 0; i < n; i++) {
            st[i][0] = ob[i];
        }

        for (int d = 1;  d < Log; d++) {
            for (int i = 0; i + (1 << (d - 1)) < n; i++) {
                st[i][d] = max(st[i][d - 1], st[i + (1 << (d - 1))][d - 1]);
            }
        }
    }

    ll ask(int l, int r) {
        if (l > r) {
          	// 注意
            return 1e18;
        }
        int d = 31 - __builtin_clz(r - l + 1);
        return max(st[l][d], st[r - (1 << d) + 1][d]);
    }
};
```



##### 四 最小值 min

```cpp
static constexpr int maxn = 100010;
struct RMQMIN {
    int Log;
    int n;
    vector<vector<ll>> st;
    // 采用vector<RMQ>时，可以将其改造成init方法
    RMQMIN(vector<ll>& ob) {
        n = ob.size();
        // Log 存储n以内最大的二次幂指数，实在不行可以设置成最大 32
        Log = 32 - __builtin_clz(n);
        // 这里一定不能使用resize，resize不会更新已有范围
        st.assign(n, vector<ll>(Log, -1));
        for (int i = 0; i < n; i++) {
            st[i][0] = ob[i];
        }

        for (int d = 1;  d < Log; d++) {
            for (int i = 0; i + (1 << (d - 1)) < n; i++) {
                st[i][d] = min(st[i][d - 1], st[i + (1 << (d - 1))][d - 1]);
            }
        }
    }

    ll ask(int l, int r) {
        if (l > r) {
          	// 注意
            return -1;
        }
        int d = 31 - __builtin_clz(r - l + 1);
        return min(st[l][d], st[r - (1 << d) + 1][d]);
    }
};
```





##### 五 最大公因数 gcd

```cpp
static constexpr int maxn = 1010;
struct RMQ {
    int Log;
    int n;
    vector<vector<int>> st;
    RMQ(vector<int>& ob) {
        n = ob.size();
        // Log 存储n以内最大的二次幂指数，实在不行可以设置成最大 32
        Log = 32 - __builtin_clz(n);
        // 这里一定不能使用resize，resize不会更新已有范围
        st.assign(n, vector<int>(Log, -1));
        for (int i = 0; i < n; i++) {
            st[i][0] = ob[i];
        }

        for (int d = 1;  d < Log; d++) {
            for (int i = 0; i + (1 << (d - 1)) < n; i++) {
                st[i][d] = gcd(st[i][d - 1], st[i + (1 << (d - 1))][d - 1]);
            }
        }
    }

    int ask(int l, int r) {
        if (l > r) {
            return -1;
        }
        int d = 31 - __builtin_clz(r - l + 1);
        return gcd(st[l][d], st[r - (1 << d) + 1][d]);
    }
};
```



##### 六 最小公倍数 lcm

