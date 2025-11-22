### Title 区间或 和 区间或 的异或

##### 1 emphasis

- 区间或 和 区间或 的异或



##### 2 key points

 **设 $xor$表示区间 按位与$and$ $\bigoplus$ 按位或$or$，则 $xor$ 随着区间的增大是单调不减的**

> 证明：
>
> a. 不难发现，随着区间的扩大，区间按位与$and$是单调不增的
>
> b. 不难发现，随着区间的扩大，区间按位或$or$是单调不减的
>
> c. 且存在一个很明显的结论，区间按位与$and$ 一定是 区间按位或$or$的子集
>
> 设 $and = 11111000$，$or = 11111001$
>
> 对于$and$，只可能由$1$变$0$
>
> 对于$or$，只可能由$0$变$1$
>
> 所以，考虑单个bit：
>
> scene1： 假设 $and$ 和 $or$ 都是 $1$ ，随着区间增大，只可能 $and$ 的该位由 $1$ 变 $0$，此时 $xor$ 增大
>
> scene2：假设 and 和 or 都是 0 ，随着区间增大，只可能 $or$ 的该位由$0$变$1$，此时$xor$增大
>
> scene3：假设 $and$ 是 $0$ ，$or$ 是 $1$ ，随着区间增大，$and$ 和 $or$ 都不会发生变化，所以 $xor$ 不变
>
> **综上发现，逐位单调；所以随着区间的扩大，$xor$ 单调不减，得证！**



##### 3 thought



##### 4 code

```cpp
void test_1(vector<int>& a) {
    int n = a.size();
    int m_or, m_and, m_xor;
    for (int i = 0; i < n; i++) {
        if (i == 0) {
            m_or = m_and = a[i];
        } else {
            m_or |= a[i];
            m_and &= a[i];
        }
        m_xor = m_or ^ m_and;
        cout << m_and << " " << m_or << " " << m_xor << "\n";
    }
}

void solve() {
    vector<int> a = {8, 12, 4};
    test_1(a);
}
```



##### 5 summary

