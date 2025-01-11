### Title  满足$gcd \cdot lcm = 乘积$的子数组

##### 1 emphasis

- gcd公式
- lcm公式
- 最大公约数 乘以 最小公倍数 等于 乘积 的数组



##### 2 key points

 给定一个数组，计算满足一下条件的子数组的最大长度

$gcd(arr) \times lcm(arr) = mul(arr)$

其中， $gcd$表示子数组的最大公约数，$lcm$表示子数组的最小公倍数，$mul$表示子数组的乘积



##### 3 thought

**定理：要使得一个数组的 $gcd \times lcm = mul$，这个数组里的元素必须两两互质或者数组的大小为2**

> 证明：
>
> scene 1：如果数组大小为2
>
> 一定满足；很明显，对于两个元素，他们的 乘积 等于 最大公约数 乘以 最小公倍数
>
> scene 2: 如果数组大小$\ge 3$
>
> 假设对于数组$a=(a_0,a_1,a_2,...,a_n)$满足 $gcd \times lcm = mul$
>
> 现在考虑对$mul$进行质因数分解，假设存在质因数 $e_0,e_1,e_2,...,e_k$
>
> 再考虑单个质因数$e$:
>
> - 对于$gcd(a)$：$e$的个数为$a$中所有元素质因数分解后的$e$数量的**最小值**，即 $min\{{a_0}_{e},{a_1}_{e},...,{a_n}_{e}\}$
> - 对于$lcm(a)$：$e$的个数为$a$中所有元素质因数分解后的$e$数量的**最大值**，即 $max\{{a_0}_{e},{a_1}_{e},...,{a_n}_{e}\}$
> - 对于$mul(a)$：$e$的个数为$a$中所有元素质因数分解后的$e$数量的**和**，即 $sum\{{a_0}_{e},{a_1}_{e},...,{a_n}_{e}\}$
>
> 要使得 $min\{{a_0}_{e},{a_1}_{e},...,{a_n}_{e}\} + max\{{a_0}_{e},{a_1}_{e},...,{a_n}_{e}\} = sum\{{a_0}_{e},{a_1}_{e},...,{a_n}_{e}\}$ 
>
> **则 ${a_0}_{e},{a_1}_{e},...,{a_n}_{e}\}$中必须至多只有一个大于1，其他的全部等于0，即非零的e不能超过1个**，否则上式不可能相等
>
> 得证！



##### 4 code

- 数据较大时，可能会TLE

```cpp
class Solution {
public:
    // 质因数分解
    void exe(int x, set<int>& mp) {
        for (int i = 2; i * i <= x; i++) {
            if (x % i == 0) {
                while (x % i == 0) {
                    x /= i;
                }
                mp.insert(i);
            }
        }
        if (x > 1) {
            mp.insert(x);
        }
    }
    
    int maxLength(vector<int>& a) {
        int n = a.size();
        // 两个数的子数组一定满足
        int ans = 2;
        
        // 因数出现的次数
        map<int, int> mp;
        // 出现次数超过2的因数
        set<int> ov;
        
        int l = 0, r = 0;
        while (r < n) {
            set<int> st1;
            exe(a[r], st1);
            for (auto& e : st1) {
                mp[e]++;
                if (mp[e] > 1) {
                    ov.insert(e);
                }
            }

            while (l <= r && ov.size()) {
                set<int> st2;
                exe(a[l], st2);
                for (auto& e : st2) {
                    mp[e]--;
                    if (mp[e] == 1) {
                        ov.erase(e);
                    }
                }
                l++;
            }

            ans = max(ans, r - l + 1);
            r++;
        }
        return ans;
    }
};
```



##### 5 summary

