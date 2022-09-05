1. 负数取模
ans: 负数取模依然是负数，如 -1 % 4 = -1，要解决需要代码：
```c++
int modab(int a, int b) {
	while (a < 0) {
		a += b;
	}
	return a % b;
}
```

2. 数组离散化


3. 二进制子集包含 
如果要证明mask1是否有mask2的二进制子集，只需要判断`mask1 & mask2 == mask1`是否成立即可