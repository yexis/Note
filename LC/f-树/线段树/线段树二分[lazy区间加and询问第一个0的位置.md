### Title 区间加 and 询问第一个0的位置

##### 1 emphasis

- 线段树二分
- 区间加 and 询问第一个0的位置



##### 2 key points

**前提条件**

**初始时所有元素 ≥ 0，且区间加法不会让元素变成负数。**





##### 3 thought



##### 4 code

例题：[小红的区间构造](https://ac.nowcoder.com/acm/contest/120553/E)

```cpp
struct Node {
    int mi;
    int c;
    int z;
    Node() {
        mi = 0;
        c = 0;
        z = 0;
    }
    Node(int x) {
        mi = x;
        c = 1;
        z = 0;
    }
    Node operator+(const Node& b) const {
        Node res;
        if (mi < b.mi) {
            res.mi = mi;
            res.c = c;
        } else if (mi > b.mi) {
            res.mi = b.mi;
            res.c = b.c;
        } else {
            res.mi = mi;
            res.c = c + b.c;
        }
        return res;
    }
};

struct Seg {
    vector<int> a;
    Node tr[800010];
    Seg(vector<int> _a) {
        a = _a;
    }
    void build(int o, int l, int r) {
        if (l == r) {
            tr[o] = Node(a[l]);
            return;
        }
        int m = (l + r) >> 1;
        build(o * 2, l, m);
        build(o * 2 + 1, m + 1, r);
        push_up(o);
    }
    void push_down(int o) {
        if (tr[o].z) {
            tr[o * 2].mi += tr[o].z;
            tr[o * 2].z += tr[o].z;

            tr[o * 2 + 1].mi += tr[o].z;
            tr[o * 2 + 1].z += tr[o].z;
            tr[o].z = 0;
        }
    }
    void push_up(int o) {
        tr[o] = tr[o * 2] + tr[o * 2 + 1];
    }
    void add(int o, int l, int r, int L, int R, int u) {
        if (L <= l && R >= r) {
            tr[o].mi += u;
            tr[o].z += u;
            return;
        }

        push_down(o);
        int m = (l + r) >> 1;
        if (L <= m) {
            add(o * 2, l, m, L, R, u);
        }
        if (R > m) {
            add(o * 2 + 1, m + 1, r, L, R, u);
        }
        push_up(o);
    }
    // 返回 [L, R]中第一个0的位置
    int ask(int o, int l, int r, int L, int R) {
        if (l > R || r < L || tr[o].mi > 0) {
            return -1;
        }
        if (l == r) return l;

        push_down(o);
        int m = (l + r) >> 1;
        int idx = ask(o * 2, l, m, L, R);
        if (idx < 0) {
            return ask(o * 2 + 1, m + 1, r, L, R);
        }
        return idx;
    }
};
```



##### 5 summary

