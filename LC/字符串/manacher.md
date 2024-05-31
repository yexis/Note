##### 模板

```cpp
namespace MANACHER_1 {
    vector<int> get_d1(string s) {
        int n = s.size();
        vector<int> d(n);
        int l = 0, r = -1;
        for (int i = 0; i < n; i++) {
            int k = i > r ? 1 : min(d[l + r - i], r - i + 1);
            while (i - k >= 0 && i + k < n && s[i - k] == s[i + k]) {
                k++;
            }
            d[i] = k--;
            if (i + k > r) {
                r = i + k;
                l = i - k;
            }
        }
        return d;
    }
    vector<int> get_d2(string s) {
        int n = s.size();
        vector<int> d(n);
        int l = 0, r = -1;
        for (int i = 0; i < n; i++) {
            int k = i > r ? 0 : min(d[l + r - i], r - i + 1);
            while (i - k - 1 >= 0 && i + k < n && s[i - k - 1] == s[i + k]) {
                k++;
            }
            d[i] = k--;
            if (i + k > r) {
                r = i + k;
                l = i - k - 1;
            }
        }
        return d;
    }
}
```

