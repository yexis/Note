##### 定义

对于一个长度为$n$的字符串，定义函数$z[i]$表示$s$和$s[i,n-1]$（即以$s[i]$开头的后缀）的最长公共前缀（$LCP$）的长度。$z$被称为$s$的$Z$函数。也称为扩展KMP。特别地，$z[0] = 0$。



##### 模板 

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



##### 模板2

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

