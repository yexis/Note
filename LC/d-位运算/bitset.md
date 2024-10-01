> bitset
>
> 适用于dp结果只有0和1的情况，即true or false



### 常用操作

bitset左侧位高位，右侧位低位，且下标从0开始

```cpp
a.size() // a的位数
a.any()  //a中是否含1
a.none()  //a是否全为0
a.all() // a是否全为1
a.count()  //a中有几个1
a.[pos]  //访问第pos位
a.test(pos)  //第pos位是否为1
a.set()  //全部设为1
a.reset()  //全部清零
a.flip()  //全部取反
a.to_ulong()  //转成32位无符号整数
a.to_ullong() // 转换为64位无符号整数，如果超出范围则报错
a.to_string() // 转换为string
```



### 位操作

```cpp
a|b
a&b
a^b
~a
a<<1
a>>1
```



### 保留bitset后n位（即[n-1,n-2,n-3,...,0]位）

```cpp
bitset<1000> f;
int n = 100;
int l = f.size() - n;
f = f << l >> l;
```



### 利用bitset实现进制转换

```cpp
// 二进制转十进制
#include<bits/stdc++.h>
using namespace std;
int main()
{
    bitset<16> a; // 16 bit 二进制数据，还有 bitset<32>
    cin >> a;
    cout << a.to_ulong() << endl;
    return 0;
}

// 十进制转二进制
#include<bits/stdc++.h>
using namespace std;
int main()
{
    int b;
    cin >> b;
    bitset<16> a(b); // 16 bit 二进制数据，还有 bitset<32>
    cout << a<< endl;
    return 0;
}
```

