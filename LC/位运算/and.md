### 一 计算所有子数组按位与的结果

- logTrick
- logtrick



1.2 性质

> 按位与的结果会随着区间$[l, r]$的增大，单调不增
>
> 1. 当固定$l$时，随着$r$的增大，区间按位与结果不可能增加，要么减小、要么不变
> 2. 当固定$r$时，随着$l$的减小，区间按位与结果不可能增加，要么减小，要么不变
> 3.   关于区间按位与主题相关的问题，根据题意，合理设置固定$l$或者固定$r$



**1.2 适用场景**

1. 求出**所有**子数组按位或的结果，以及值等于该结果的子数组的个数
2. 求按位或结果等于**任意给定数字**的子数组的最短长度/最长长度



```cpp
// ands: 记录子数组的的按位与结果 以及拥有相同值子数组右端点的最小值
vector<pair<int, int> > ands;

void cal(vector<int>& nums) {
  ands.clear();
  int n = nums.size();

  for (int i = n - 1; i >= 0; i--) {
    int num = nums[i];
    ands.emplace_back(num, i);
    ands[0].first &= num;

    int k = 0;
    for (int j = 1; j < ands.size(); j++) {
      ands[j].first &= num;
      if (ands[k].first == ands[j].first) {
        ands[k].second = ands[j].second;
      } else {
        ands[++k] = ands[j];
      }
    }
    ands.resize(k + 1);
    // 根据题目条件设置
    for (auto& a : ands) {
      mi = min(mi, abs(a.first - target));
    }
  }
```

