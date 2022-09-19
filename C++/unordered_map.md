### 1. 对key的要求

std::unordered_map 对$key$的要求：存在$hash$函数

std::map 对$key$要求：支持比较函数，即$==$符号



### 2. unordered_map+pair

> Q : 为什么map<key, value>  key支持pair类型，而unordered_map<key,value>不支持？
>
> A : 与两类map的实现有关，std::map底层是红黑树，只需要key支持==即可；std::unordered_map底层是hash实现，即key必须是支持hash方法的类型，C++中pari默认并不支持hash。

将pair设置成unordered_map的$key$，即`unordered_map<pair<int, int>, int> um`

```cpp
// 自定义pair的哈希函数
auto pair_hash = [fn = hash<int>()](const pair<int, int>& o) -> size_t {
  return (fn(o.first) << 16) ^ fn(o.second);
};

// 或者 简单点
auto pair_hash = [](const pair<int, int>& o) -> size_t {
  return (o.first << 16) ^ o.second;
};
unordered_map<pair<int, int>, int, decltype(pair_hash)> um(0, pair_hash);
```

