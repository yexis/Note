### 1 小顶堆
```cpp
// 1. 按元素值排序
priority_queue<int, vector<int>, greater<> > pq;

// 2. 自定义排序规则
// 这个地方不太理解为什么是 【nums[a] > nums[b] -> 小顶堆】
auto cmp = [&](const auto& a, const auto& b) {
  return nums[a] > nums[b];
}
proirity_queue<int, vector<int>, decltype(cmp)> pq(cmp);


// 写法2
struct node {
    double d;
    int mask;
    int stg;
    int par;
};
struct cmp {
    bool operator()(const node& a, const node& b) {
        return a.d > b.d;  // 小的先出队，小顶堆
	      // return a.d < b.d;  // 大顶堆
    }
};

priority_queue<node, vector<node>, cmp> pq;
```



### 2 大顶堆
```cpp
// 1. 按元素值排序 或者默认就行
priority_queue<int, vector<int>, less<> > pq;
priority_queue<int> pq;

// 2. 自定义排序规则
// 同样，这个地方也不太理解为什么是 【nums[a] < nums[b] -> 大顶堆】
auto cmp = [&](const auto& a, const auto& b) {
  return nums[a] < nums[b];
}
proirity_queue<int, vector<int>, decltype(cmp)> pq(cmp);
```



### 3 自定义
##### 3.1 自定义比较方法
```cpp
// 写法一
auto cmp = [&](const auto& a, const auto& b) {
    if (a.second == b.second) {
        return a.first < b.first;
    }
    return a.second > b.second;
};

// 写法二
function<bool(const pair<int, int>&, const pair<int, int>&)> cmp = [&](const auto& a, const auto& b) {
        if (a.first == b.first) {
            return a.second < b.second;
        }
        return a.first < b.first;
    };

```

##### 3.2 自定义类 重载 "<" 符号
```cpp
class Node {
public:
    int a;
    int b;
    // 构造函数，供emplace使用
    Node(int _a, int _b) : a(_a), b(_b) {}
    const bool operator < (const Node& that) const {
        if (a == that.a) {
            return b < that.b;
        }
        return a < that.a;
    }
};
```
