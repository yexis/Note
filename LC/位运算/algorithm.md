### 1. C++自带

```cpp
// 计算n的二进制表示中，有多少个前导0
__builtin_clz(n);
__builtin_clzll(n);

// 计算n的二进制表示中，最高位的1出现在第几位
31 - __builtin_clz(n);

// 计算n的二进制表示中，从最低位开始(右起)的连续的0的个数
__builtin_ctz(n);
__builtin_ctzll(n);

// 统计二进制中有多少个1
__builtin_popcount()
__builtin_popcountll()

```

```cpp
// 计算n的二进制表示中，从最低位开始(右起)的连续的0的个数
__builtin_ctz(n);
// 如
__builtin_ctz(8) = 3
```
