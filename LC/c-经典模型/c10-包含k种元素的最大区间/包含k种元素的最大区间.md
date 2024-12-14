### Title 包含k种元素的最大区间(枚举k + 滑动窗口)

##### 1 emphasis

- 包含k种元素的最大区间
- 包含k类元素的最大区间
- 从点$i$出发，包含$k$种不同元素的最远位置



##### 2 key points

###### 2.1 建模

给定一个数组$a$，对于数组$a$中的每个位置$i$，计算从$i$出发最多包含$k$个元素能达到的最远位置$j$；即找到最大的$j$，使得区间$[i,j]$最多包含$k$个不同元素

> 例题：[漫长的小纸带(滑窗+DP)](https://www.luogu.com.cn/problem/U508233?contestId=183429)

```cpp
void win(vector<int>& a, int d, vector<int>& g) {
    // 假设a中的元素均不大于n；
    // 因为考虑的是元素的种类，所以和元素实际的值没有关系
    // 如果a中的元素大于n，可以先进行离散化
    int n = a.size();
    int cur = 0;
    vector<int> cnt(n + 1); 
    for (int l = 0, r = -1; l < n; l++) {
        while (r < n && cur <= d) {
            r++;
            if (r >= n) break;
            if (cnt[r]++ == 0) {
                cur++;
            }
        }
        // g[l]表示从l位置出发，在最多包含k个不同元素的前提下能到达的最远位置
        g[l] = r - 1;
        if (--cnt[a[l]] == 0) {
            cur--;
        }
    }
}
```





##### 3 thought



##### 4 code



##### 5 summary

