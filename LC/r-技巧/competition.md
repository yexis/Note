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

### 2. 数组离散化模板

```cpp
// 方法1：vector操作
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

```cpp
// 方法2：数组操作
int b[200010];
sort(b, b + n);  // 排序
int zn = unique(b, b + n) - b;  // 去重，zn 为去重后数组的长度
auto get_id = [&](int x) -> int {
  return lower_bound(b, b + zn, x) - b;
};
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

### 5. 二的幂次定理

假设存在若干个$2$的幂次的数组成的集合，记为$S$，存在另外一个$2$的幂次的数，记为$b$，且集合$S$中的每个元素均**小于数**$b$，且集合$S$中所有元素的和**大于等于**$b$，那么集合$S$中一定存在一个子序列的和等于$b$

