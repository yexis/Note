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