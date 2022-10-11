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
// 会改变原数组
// 如果不想改变原数组，复制数组后调用 discretize
void discretize(vector<int>& ob) {
  vector<int> b = ob;
  sort(b.begin(), b.end());
  b.erase(unique(b.begin(), b.end()), b.end());
  for (auto& v : ob) {
    v = lower_bound(b.begin(), b.end(), v) - b.begin() + 1;
  }
}
```

### 3. 判断一个字符串/数组中所有元素都相等

```cpp
equal(s.begin() + 1, s.end(), s.begin());
```

