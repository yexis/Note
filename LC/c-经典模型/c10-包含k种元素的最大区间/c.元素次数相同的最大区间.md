### Title 元素次数相同的最大区间

##### 1 emphasis

- 求最长的平衡子串
- 求元素次数相同的最大区间



##### 2 key points

要求区间内不同字符的出现次数相同，那么区间内所有字符出现的次数之差均为0



核心思想：**次数相等 转化为 次数差=0**



场景一：仅2种字符

方法①  +1/-1赋值法：找到最长的和为0的区间即可

方法② 使用次数差 进行哈希 

假设满足条件的区间为$$[l,r]$$，则

$a_{r+1} - a_l = b_{r+1} - b_l$

移项得 $a_{r+1} - b_{r+1} = a_l - b_l$

令 $\delta = a - b$

使用hash 记录个数差$\delta$的前缀和，找出最远的两个前缀和相同的位置即可，即为答案



场景二：3种字符，对2种字符的场景进行延伸

假设为$a,b,c$ 3种字符，要使区间内3种元素的出现次数均相同

不妨两两考虑，由等式传递性得，区间满足$cnt_a - cnt_b = 0 \ \ \&\& \ \ cnt_a - cnt_c = 0$

同样使用hash进行维护，2种字符的场景hash的key维护的是2种元素的差，则3种字符的场景hash的key需要维护2个2种元素的差，定义 `map<pair<int,int>, int>> mp`



##### 3 thought



##### 4 code

```cpp
// 2种字符的场景
auto calc = [&](string& s, char a, char b) {
    int m = s.size();
    using ll = long long;
  	// 记录x出现的第一个位置
    unordered_map<ll, int> mp;
    mp[0] = 0;
    // x 代表前缀中字符a的出现次数
    // y 代表前缀中字符b的出现次数
    int x = 0, y = 0;
    for (int i = 0; i < m; i++) {
        if (s[i] == a) x++;
        else if (s[i] == b) y++;
        ll key = x - y;
        if (mp.count(key)) {
            ans = max(ans, i + 1 - mp[key]);
        } else {
            mp[x - y] = i + 1;
        }
    }
};

// 3种字符的场景
auto cal3 = [&](string& s) {
    // (x - y, x - z)
    using ll = long long;
    using pii = pair<int, int>;
    // 记录x出现的第一个位置
    map<pii, int> mp;
    mp[{0, 0}] = 0;
    // x 代表前缀中字符a的出现次数
    // y 代表前缀中字符b的出现次数
    // z 代表前缀中字符c的出现次数
    int x = 0, y = 0, z = 0;
    for(int i = 0; i < n; i++) {
        if (s[i] == 'a') x++;
        else if (s[i] == 'b') y++;
        else if (s[i] == 'c') z++;
        pii key = pii(x - y, x - z);
        if (mp.count(key)) {
            ans = max(ans, i + 1 - mp[key]);
        } else {
            mp[key] = i + 1;
        }
    }
};

// 4种字符的场景类似，维护3个次数差
...
```



##### 5 summary

