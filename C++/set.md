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
struct Node{
public:
    int a;
    string s;
    Node(const int& aa, const string& ss) {
        a = aa;
        s = ss;
    }
    // 函数最后必须是const吗？不加const为啥无法通过？
    bool operator < (const Node& b) const {
        if (a == b.a) {
          return s > b.s;
        }
        return a < b.a;
    };
};
```

##### 1.3 自定义排序规则
```cpp
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
```