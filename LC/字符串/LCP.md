### 一 LCP



### 二 使用场景

#### 2.1 如何判断字符串中两段字符串相等

这是一个典型的套路，令 $lcp[i][j]$ 表示字符串 $s$ 中，从下标$i$开始的子字符串 $i$ 和以下标 $j$ 开始的子字符串 $s[j]$ 的最长公共前缀的长度。

则状态转移方程是

1. $s[i] \neq s[j] \rightarrow lcp[[i][j] = 0$

2. $s[i] = s[j] \rightarrow lcp[i][j] = lcp[i + 1][j + 1] + 1$

要判断字符串从 $i$ 开始的连续两段长度为 $len$ 的字符串相等，只需要判断

$lcp[i][i + len] \geq len$

代码：

```cpp
vector<vector<int> > lc;
void lcp(string s) {
  int n = s.size();
  lc = vector<vector<int> >(n + 1, vector<int>(n + ));
  for (int i = n - 1; i >= 0; i--) {
    for (int j = n - 1; j > i; j--) {
      if (s[i] == s[j]) {
        lc[i][j] = lc[i + 1][j + 1] + 1;
      }
    }
    ++lc[i];
  }
}
```

