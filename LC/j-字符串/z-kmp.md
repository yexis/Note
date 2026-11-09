### Title Z函数

##### 1 emphasis

* 扩展kmp
* z-kmp
* z函数



##### 2 examples



##### 3 key points

###### 3.1  定义

> 函数$z[i]$表示$s$和$s[i,n-1]$（即以$s[i]$开头的后缀）的最长公共前缀（$LCP$）的长度，特别地，$z[0] = 0$。

对于一个长度为$n$的字符串，定义函数$z[i]$表示$s$和$s[i,n-1]$（即以$s[i]$开头的后缀）的最长公共前缀（$LCP$）的长度。$z$被称为$s$的$Z$函数。也称为扩展KMP。特别地，$z[0] = 0$。



###### 3.2 Z-KMP

**计算字符串$s$和$s[i,n-1]$的最大匹配长度，即 $max \{ k \ | s[0,n-1] = s[i,n-1] \}$**



##### 4 thought

###### 4.1 使用场景

1. 字符串$s$和$t$，计算对于$s$的任意的下标$i$，将$s[0:i]$翻转后与$t$的最长公共前缀长度$lcp$

   

##### 5 code

###### 5.1 模板1

```cpp
namespace Z_1 {
    vector<int> z_kmp(string& s) {
        int n = s.size();
        vector<int> z(n);
        int l = 0, r = 0;
        for (int i = 1; i < n; i++) {
            z[i] = max(0, min(z[i - l], r - i + 1));
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
                l = i;
                r = i + z[i] - 1;
                z[i]++;
            }
        }
        return z;
    }
}
```



###### 5.2 模板2

```cpp
namespace Z_1 {
    vector<int> z_kmp(string& s) {
        int n = s.size();
        vector<int> z(n);
        int l = 0, r = 0;
        for (int i = 1; i < n; i++) {
            if (i <= r && z[i - l] < r - i + 1) {
                z[i] = z[i - l];
            } else {
                z[i] = max(0, r - i + 1);
                while (i + z[i] < n && s[z[i]] == s[i + z[i]]) ++z[i];
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
        }
        return z;
    }
}
```



##### 6 summar

