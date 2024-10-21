> - 最长递增子序列
>
> - LIS



##### 最长递增子序列 （严格递增）

```cpp
int lis(vector<int>& a) {
    int n = a.size();
    vector<int> v;
    for (int i = 0; i < n; i++) {
        int k = lower_bound(v.begin(), v.end(), a[i]) - v.begin();
        if (k == v.size()) {
            v.emplace_back(a[i]);
        } else {
            v[k] = a[i];
        }
    }
    return v.size();
}
```



##### 例题

- [3288. 最长上升路径的长度](https://leetcode.cn/problems/length-of-the-longest-increasing-path/)

