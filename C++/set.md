## set 讲解 

* set详解

  

### 一 自定义排序

##### 1.1 简单升序/降序
```cpp
// 类型重载了"<"即可
// 升序
set<int> st;
// 降序
set<int, greater<>> st;
```

##### 1.2 定义结构体 重载"<"符号
```cpp
class Node {
public:
    int a;
    int b;
    Node(int _a, int _b) : a(_a), b(_b) {}
    const bool operator < (const Node& that) const {
        if (a == that.a) {
            return b < that.b;
        }
        return a < that.a;
    }
};
```

##### 1.3 自定义排序方法
```cpp
// 写法一
function<bool(const pair<int, int>&, const pair<int, int>&)> cmp = [&](const auto& a, const auto& b) {
    if (a.first == b.first) {
        return a.second < b.second;
    }
    return a.first < b.first;
};

// 写法二
auto cmp = [&](const auto& a, const auto& b) {
    if (a.second == b.second) {
        return a.first < b.first;
    }
    return a.second > b.second;
};
set<pair<int, int>, decltype(cmp)> st(cmp);
```

##### 1.4 定义结构体 重载()符号 生成仿函数
```cpp
class Node {
public:
    int a;
    int b;
    Node(int _a, int _b) : a(_a), b(_b) {}
};
struct cmp {
    const bool operator() (const Node& x, const Node& y) {
        if (x.a == y.a) {
            return x.b > y.b;
        }
        return x.a > y.a;
    }
};
void test_set() {
    set<Node, cmp> st;
    st.emplace(0, 0);
    st.emplace(1, 1);
    st.emplace(2, 2);
    for (auto [d, u] : st) {
        cout << d << " " << u << endl;
    }
}
```

##### 1.5 set lower_bound时如何判断两边都有元素

```cpp
set<int> st;
auto it = st.lower_bound(x);
if (it != st.begin() && next(it) != st.end()) {
    // 两边都存在元素
    int l = *prev(it), r = *next(it);
} else if (it != st.begin()) {
    // 左边没有元素，但是右边有元素
    int l = *prev(it);
} else if(next(it) != st.end()) {
    // 右边没有元素，但是左边有元素
    int r = *next(it);
} else {

}
```

