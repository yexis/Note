### Title 区间改 and 询问区间和

##### 1 emphasis

- lazy线段树



##### 2 key points

 

##### 3 thought



##### 4 code

```cpp
/*
 * https://ac.nowcoder.com/acm/contest/120553/F
 * 
 * - 该区间的两个端点不能与任意其他区间的端点重合
 * - 找出覆盖当前点的区间的左端点的最大值
*/

struct Node {
    int len;
    int sum;
    int z;
    Node() {
        // z = -1代表默认值，代表不需要操作
        // 这里为什么不默认取0，因为存在将区间改0的操作
        // z的默认值应该 区别于 所有可能值
        len = 0, sum = 0, z = -1;
    }
    Node(int u, int l) {
        sum = u * l, len = l, z = u;
    }
    Node operator+(const Node& b) const {
        Node res;
        res.len = len + b.len;
        res.sum = sum + b.sum;
        res.z = -1;
        return res;
    }
};
struct Seg {
    Node tr[800100];
    Seg() {

    }
    void push_up(int o) {
        tr[o] = tr[o * 2] + tr[o * 2 + 1];
    }
    void push_down(int o) {
        if (tr[o].z != -1) {
            tr[o * 2].sum = tr[o].z * tr[o * 2].len;
            tr[o * 2].z = tr[o].z;
            tr[o * 2 + 1].sum = tr[o].z * tr[o * 2 + 1].len;
            tr[o * 2 + 1].z = tr[o].z;
            tr[o].z = 0;
        }
    }
    void build(int o, int l, int r) {
        if (l == r) {
            tr[o] = Node();
            tr[o].len = 1;
            tr[o].z = 0;
            return;
        }
        int m = (l + r) >> 1;
        build(o * 2, l, m);
        build(o * 2 + 1, m + 1, r);
        push_up(o);
    }
    void add(int o, int l, int r, int L, int R, int u) {
        if (l >= L && r <= R) {
            int len = r - l + 1;
            tr[o] = Node(len * u, len);
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
    Node ask(int o, int l, int r, int L, int R) {
        if (l > r) return Node();
        if (l >= L && r <= R) {
            return tr[o];
        }
        push_down(o);
        Node ans;
        int m = (l + r) >> 1;
        if (L <= m) {
            ans = ans + ask(o * 2, l, m, L, R);
        }
        if (R > m) {
            ans = ans + ask(o * 2 + 1, m + 1, r, L, R);
        }
        return ans;
    }
};
```



##### 5 summary

