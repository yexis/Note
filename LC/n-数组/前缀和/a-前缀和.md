##### 两两间距离

给定一个数组，数组中元素代表数轴上的一个点，如何计算数组中所有点两两之间的距离之和

```cpp
// 前缀和
// 遍历每个右端点，统计其左侧所有点分别作为左端点时的距离
// 即 a[i] * i - sum(前缀和)
func calc(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    int ans = 0;
    int sum = 0;
    for (int i = 0; i < n; i++) {
      ans += nums[i] * i - sum;
      sum += nums[i];
    }
    return ans;
}
```

