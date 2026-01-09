##### 1 要求区间内所有元素出现次数相同，求区间最大长度

```cpp
- 等于 元素个数 * 众数频率 = 区间长度
- 一般字符种类不会太多，对于字符种类不太多的场景，直接枚举所有情况；
```





##### 2 function用法

```cpp
auto dfs = [&](auto&& dfs, int u, int o) -> void {}
要比
function(void(int, int)) dfs = [&](int u, int o) -> void {}
快很多
```

