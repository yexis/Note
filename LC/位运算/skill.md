### 1. 二进制包含

```cpp
// 如果要证明mask1是否有mask2的二进制子集，只需要判断 mask1 & mask2 == mask1 是否成立即可
bool contain(int mask1, mask2) {
  return (mask1 & mask2) == mask2;
}
```

