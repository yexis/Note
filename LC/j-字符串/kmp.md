### Title KMP

##### 1 emphasis

- KMP
- 字符串KMP



##### 2 key points

给定一个长度为$n$的字符串$s$，其真前缀函数被定义为一个长度为$n$的数组$\pi$，其中$\pi[i]$的定义是：

- 如果子串$s[0...i]$有一对相等的真前缀和真后缀：$s[0...k-1]=s[i-(k-1)...i]$；那么$\pi[i]$是这对相等的前后缀的长度，$\pi[i]=k$；
- 如果不止一对真前后缀相等，那么$\pi[i]$是其中最大的长度；
- 如果没有相等的真前后缀，那么$pi[i]=0$



 

##### 3 thought



##### 4 code

##### 模板

```cpp
namespace KMP_1 {
    vector<int> kmp(string s) {
        int n = s.size();
        vector<int> p(n);
        for (int i = 1; i < n; i++) {
            int j = p[i - 1];
            while (j > 0 && s[j] != s[i]) {
                j = p[j - 1];
            }
            if (s[i] == s[j]) j++;
            p[i] = j;
        }
        return p;
    }
}
```