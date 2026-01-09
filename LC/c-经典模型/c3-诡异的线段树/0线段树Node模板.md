### Title 线段树模板（Node版本）

##### 1 emphasis



##### 2 key points

 

##### 3 thought



##### 4 code

```cpp
struct Node {
    int z;
    int v;
    Node() { v = 0; z = 0; }
    Node(int _v) { v = _v; z = 0; }
    // called by push_down
    void op() {

    }
};
Node operator+(const Node& a, const Node& b) {
    Node res;
    res.v = max(a.v, b.v);
    return res;
}

struct SegTree {
    vector<int> a;
    Node tr[800000];
    SegTree(vector<int> _a) {
        a = _a;
    }
    void push_up(int o, int l, int r) {
        tr[o] = tr[o * 2] + tr[o * 2 + 1];
    }
    void push_down(int o, int l, int r) {
        if (tr[o].z) {
            tr[o * 2].op();
            tr[o * 2].z = tr[o].z;
            tr[o * 2 + 1].op();
            tr[o * 2 + 1].z = tr[o].z;
            tr[o].z = 0;
        }
    }
    void build(int o, int l, int r) {
        if (l == r) {
            tr[o] = Node(a[l - 1]);
            return;
        }
        int m = (l + r) >> 1;
        build(o * 2, l, m);
        build(o * 2 + 1, m + 1, r);
        push_up(o, l, r);
    }
    void add(int o, int l, int r, int i, int u) {
        if (l == r) {
            // upd
            tr[o] = Node(u);
            return;
        }
        int m = (l + r) >> 1;
        if (i <= m) {
            add(o * 2, l, m, i, u);
        } else {
            add(o * 2 + 1, m + 1, r, i, u);
        }
        push_up(o, l, r);
    } 
    void add_lr(int o, int l, int r, int L, int R, int u) {
        if (L <= l && R >= r) {
            // upd
            tr[o] = Node(u);
            tr[o].z = u;
            return;
        }
        push_down(o, l, r);
        int m = (l + r) >> 1;
        if (L <= m) {
            add_lr(o * 2, l, m, L, R, u);
        }
        if (R > m) {
            add_lr(o * 2 + 1, m + 1, r, L, R, u);
        }
        push_up(o, l, r);
    }
    Node ask(int o, int l, int r, int L, int R) {
        if (L <= l && R >= r) {
            return tr[o];
        }
        push_down(o, l, r);
        int m = (l + r) >> 1;
        Node ans;
        if (L <= m) {
            ans = ans + ask(o * 2, l, m, L, R);
        }
        if (R > m) {
            ans = ans + ask(o * 2 + 1, m + 1, r, L, R);
        }
        return ans;
    }
    // 找到第一个等于target的下标
    int ask_index(int o, int l, int r, int L, int R, int target) {
        if (R < l || L > r || tr[o].v < target) {
            return -1;
        }
        if (l == r) {
            return l;
        }
        push_down(o, l, r);
        int m = (l + r) >> 1;
        int idx = ask_index(o * 2, l, m, L, R, target);
        if (idx == -1) {
            return ask_index(o * 2 + 1, m + 1, r, L, R, target);
        }
        return idx;
    }
};
```



##### 5 summary

 