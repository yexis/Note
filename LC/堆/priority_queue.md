**小顶堆**

```cpp
// 1. 按元素值排序
priority_queue<int, vector<int>, greater<> > pq;

// 2. 自定义排序规则
// 这个地方不太理解为什么是 【nums[a] > nums[b] -> 小顶堆】
auto cmp = [&](const auto& a, const auto& b) {
  return nums[a] > nums[b];
}
proirity_queue<int, vector<int>, decltype(cmp)> pq(cmp);

```



**大顶堆**

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



