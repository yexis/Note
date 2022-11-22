### 1. 负数取模
ans: 负数取模依然是负数，如 -1 % 4 = -1，要解决需要代码：

```c++
int modab(int a, int b) {
	while (a < 0) {
		a += b;
	}
	return a % b;
}
```

### 2. 数组离散化

```cpp
// 方法1
// 会改变原数组
// 如果不想改变原数组，复制数组后调用 discretize
void discretize(vector<int>& ob) {
  vector<int> b = ob;
  sort(b.begin(), b.end());
  b.erase(unique(b.begin(), b.end()), b.end());
  // 下标从 1 开始
  // 如果需要下标从 0 开始， 去掉后面的 +1
  for (auto& v : ob) {
    v = lower_bound(b.begin(), b.end(), v) - b.begin() + 1;
  }
}
```

### 3. 判断一个字符串/数组中所有元素都相等

```cpp
equal(s.begin() + 1, s.end(), s.begin());
```

### 4. 证明mask1是mask2的二进制子集
```cpp
bool check(int mask1, int mask2) {
  return (mask1 & mask2 == mask1);
}
```

### meet in middle
* Meet in the Middle算法有几种实现方法，搜索部分大致相同，合并答案部分有多种实现
* **使等式两边未知数个数相等或尽量均匀分布是用meet in the middle算法解决等式问题的常见方法**

在[-30000,30000]范围里，给出一组整数集合S。找到满足的六元组的总数使得：
(𝑎𝑏+𝑐)/𝑑−𝑒=𝑓
并且保证元组(𝑎,𝑏,𝑐,𝑑,𝑒,𝑓):𝑎,𝑏,𝑐,𝑑,𝑒,𝑓∈𝑆;𝑑≠0
将问题转化成𝑎𝑏+𝑐=𝑑(𝑒+𝑓) 


