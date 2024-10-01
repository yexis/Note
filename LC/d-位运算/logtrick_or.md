### 一 计算所有子数组按位或的结果

**1.1 性质**

> 按位或的结果会随着区间$[l, r]$的增大，单调不增
>
> 1. 当固定$l$时，随着$r$的增大，区间按位与结果不可能减小，要么增大、要么不变
> 2. 当固定$r$时，随着$l$的减小，区间按位与结果不可能减小，要么增大，要么不变
> 3. 关于区间按位或主题相关的问题，根据题意，合理设置固定$l$或者固定$r$



**1.2 适用场景**

1. 求出**所有**子数组按位或的结果，以及值等于该结果的子数组的个数
2. 求按位或结果等于**任意给定数字**的子数组的最短长度/最长长度



```cpp
// ors: 记录子数组的的按位或结果 以及拥有相同值子数组右端点的最小值
vector<pair<int, int> > ors;

void cal(vector<int>& arr) {
  	ors.clear();
    int n = arr.size();
    int ans = 0;
    for (int i = n - 1; i >= 0; i--) {
        int num = arr[i];
        ors.emplace_back(0, i);
        ors[0].first |= num;

        int k = 0;
        for (int j = 1; j < ors.size(); j++) {
            ors[j].first |= num;
            if (ors[k].first == ors[j].first) {
                ors[k].second = ors[j].second;
            } else {
                ors[++k] = ors[j];
            }
        }
        // ors存储以i开始的所有子数组的按位或的结果、以及子数组的右端点的最小值
        ors.resize(k + 1);
      	// 根据实际需求计算
        for (auto o : ors) {
            st.insert(o.first);
        }
    }

    return st.size();
}
```

