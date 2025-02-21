### T3 [Q3. 选择 K 个互不重叠的特殊子字符串](https://leetcode.cn/contest/weekly-contest-437/problems/select-k-disjoint-special-substrings/)

##### 1 emphasis

- 特殊子子字符串
- 动态规划 DP



##### 2 key points

给你一个长度为 n 的字符串 s 和一个整数 k，判断是否可以选择 k 个互不重叠的 特殊子字符串 。
特殊子字符串 是满足以下条件的子字符串：

子字符串中的任何字符都不应该出现在字符串其余部分中。
子字符串不能是整个字符串 s。

注意：所有 k 个子字符串必须是互不重叠的，即它们不能有任何重叠部分。

如果可以选择 k 个这样的互不重叠的特殊子字符串，则返回 true；否则返回 false。

子字符串 是字符串中的连续、非空字符序列。



##### 3 thought



##### 4 code

```cpp
/*
对于一个字符首次出现的位置和最后出现的位置分别使用l和r表示
1. 首先，满足题目条件的【最小】特殊子字符串个数一定不超过26个，【最小】即不能再小；
    - 强调最小是因为两个特殊子字符串可能是连续的，比如 acacbdbd 字符串中有3个特殊子字符串；
2. 每个特殊子字符串必定以某个字符开始(但是不一定以该字符结束，如acacbdbd)，那么统计以26个字符开始的特殊子字符串即可；
3. 对于任意的两个字符c1,c2，由他们形成的特殊子字符串一定是不相交的，即不可能出现 l1 ... l2 ... r1 ... r2，这违背了题意；
    - 所以只可能是 l1 ... l2 ... r2 ... l1，或者 l1 ... r1 ... l2 ... r2
4. 那么，如何统计这些特殊子字符串呢？
    - 很明显，特殊子字符串的起始位置一定是一个字符的首次出现的位置
    - 那么对于某个字符首次出现的位置，一定能找到一个特殊子字符串吗？答案是不一定，因为如果[l,..c2..,r]假如中间出现了c2，且c2的首次出现位置在l之前，那么从l出发就找不到特殊子字符串
    - 所以，从每个起始位置开始，看能否找到特殊子字符串，每遍历到一个字符c，更新mx=max(mx, r_c)，直到遍历到mx没再出现新字符，说明找到了一个特殊子字符串
    - 最后，就是线性DP
*/

class Solution {
public:
    bool maxSubstringLength(string s, int k) {
        int n = s.size();
        if (k == 0) return true;

        vector<set<int>> st(26);
        for (int i = 0; i < n; i++) {
            st[s[i] - 'a'].insert(i);
        }
        
        vector<int> per(n, -1);
        for (int d = 0; d < 26; d++) {
            if (st[d].size() == 0) continue;
            int be = *st[d].begin(), en = *st[d].rbegin();
            // 维护mx：表示特殊子字符串的可能的右边界
            int mx = en;
            for (int i = be; i < n; i++) {
                int dd = s[i] - 'a';
                int be1 = *st[dd].begin(), en1 = *st[dd].rbegin();
                mx = max(mx, en1);
                if (be1 < be) break;
                if (i == mx) {
                    if (i < n - 1 || *st[d].begin() > 0) {
                        per[i] = *st[d].begin();    
                    }
                    break;
                }
            }
        }
        
        vector<int> dp(n + 1);
        dp[0] = 0;
        for (int i = 1; i <= n; i++) {
            dp[i] = dp[i - 1];
            int ii = i - 1;
            if (per[ii] != -1 && per[ii] - 1 + 1 >= 0) {
                dp[i] = max(dp[i], dp[per[ii] - 1 + 1] + 1);
            }
        }
        
        return dp[n] >= k;
    }
};
```





##### 5 summary

