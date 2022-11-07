单调队列优化DP.md
### 1 key point
* 每连续 $k$ 个数的**最大/小值**
* 某个数的结果可由前 $k$ 个数中的最大/小值传递过来



### 2 带限制的子序列和

1. 序列中相邻元素 $i$ 和 $j$ 满足 $j - i <= k$
2. 计算 $max(sum)$

```cpp
int constrainedSubsetSum(vector<int>& nums, int k) {
  int n = nums.size();
  int ans = INT_MIN;

  deque<pair<int, int> > q;
  for (int i = 0; i < n; i++) {
    auto x = nums[i];
    while (!q.empty() && i - q.front().second > k) {
      q.pop_front();
    }
    // 计算以当前节点i为结尾的序列的最大值
    if (!q.empty()) {
      x = max(x, x + q.front().first);
    }
    ans = max(ans, x);

    // 保持队列单调，将小于x的队尾元素移除
    while (!q.empty() && q.back().first <= x) {
      q.pop_back();
    }
    q.emplace_back(x, i);
  }
  return ans;
}
```

