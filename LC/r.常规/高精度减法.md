##### 高精度减法

需求：如果一个数字num使用字符串表示，且这个字符串非常大，如果计算num-1

```cpp
reverse(low.begin(), low.end());
        int carry = 1;
        for (int i = 0; i < low.size(); i++) {
            if (carry) {
                if (low[i] == '0') low[i] = '9', carry = 1;
                else low[i]--, carry = 0;
            }
        }
        reverse(low.begin(), low.end());
```

