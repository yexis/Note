> * st表
>
> * rmq问题



##### 一 与运算 & 

```cpp
static constexpr int maxn = 100010;
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



##### 三 最大值 max



##### 四 最小值 min



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

