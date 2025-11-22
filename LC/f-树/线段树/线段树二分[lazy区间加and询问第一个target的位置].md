### Title 区间加 and 询问第一个target的位置

##### 1 emphasis



##### 2 key points

 **前提条件**

**区间元素连续，即 $|abs(a_i - a_{i-1})| = 1$**



##### 3 thought



##### 4 code

例题：[3721. 最长平衡子数组 II](https://leetcode.cn/problems/longest-balanced-subarray-ii/)

```cpp
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
        if (l > R || r < L || tr[o].mi > target || tr[o].mx < target) {
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
```



##### 5 summary

