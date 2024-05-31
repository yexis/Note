### 1. 二进制包含

```cpp
// 如果要证明mask1是否有mask2的二进制子集，只需要判断 mask1 & mask2 == mask1 是否成立即可
bool contain(int mask1, mask2) {
  return (mask1 & mask2) == mask2;
}
```



### 2. 计算二进制最低位的1的值  lowbit

```cpp
// 比如计算6（110）最低位的1，结果为2 
int lowbit(int x) {
  return x & -x;
}
```



### 3. 将x二进制中最低位的1变成0

```cpp
int cal(int x) {
  return x & (x - 1);
}
```



### 4. 将x二进制中最低n位的1变成0

```cpp
// 将x二进制中最低n位的1变成0
// 前提：x二进制中1的数量不少于n，即置位数(x) >= n

// 方法1
int cal(int x, int n) {
  for(int i = 0; i < n; i++) {
  	x -= lowbit(x);
	}
}

// 方法2
int cal(int x, int n) {
  for (int i = 0; i < n; i++) {
    x &= (x - 1);
  }
}
```



### 5. 将x二进制中最低位的0变成1

```cpp
int cal(int x) {
  return x | (x + 1);
}
```



### 6. 将x二进制中最低n位的0变成1

```cpp
// x = 4; 100 | 101 = 101
// x = 5; 101 | 110 = 111
// x = 7; 111 | 1000 = 1111
int cal(int x, int n) {
  for (int i = 0; i < n; i++) {
    x |= (x + 1);
  }
  return x;
}
```



### 7. 提取x二进制中最低n位1的值

```cpp
// 6(110) = 2(10) + 4(100)
// 方法1
vector<int> cal(int n) {
  vector<int> lbs;
  while (n) {
    lb = n & -n;
    lbs.push_back(lb);
    x -= lb;
  }
}

// 方法2
vector<int> cal(int n) {
  while (n) {
    int b = 0;
  	int x = 1;
    while (x * 2 <= n) {
      x <<= 1;
      b++;
    }
    n -= x;
    lbs.push_back(pow(2, b));
  }
}
```



### 8. 二进制 左右两侧0的个数

```cpp
__builtin_ctz() / __buitlin_ctzll()
// 返回元素的二进制表示末尾0的个数
// 比如 __builtin_ctz(8) = 3
// 8 = 1000 , 末尾有3个0

__buitlin_clz() / __buitlin_clzll()
// 返回元素的二进制表示前导0的个数
// 比如 __builtin_ctz(8) = 28
// 8 = 0000 0000 0000 0000 0000 0000 0000 1000 , 整型(int)为32位,有28个前导0
  
32 - __buitlin_clz()
// 表示元素的二进制的位数
```



### 9. 将'a-z'转换成'1-26'

```cpp
string s = "abcd";
for (int i = 0; i < s.size(); i++) {
  int x = s[i] & 31;
}
// s[i] & 31：保留字符 s[i] 八位二进制位的后五位。
// a ~ z 的 ASCII 码为: 011 00001 ~ 011 11010，保留后五位，即 000 00001 ~ 000 11010，正好是 1 ~ 26。
```

