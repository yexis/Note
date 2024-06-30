### multiset详解

> multiset

##### 细节一 [删除元素]

```cpp
// 方法1：
auto it = a.find(x);
if (it != a.end()) {
    a.erase(it);
}

// 方法2：
if (a.count(x)) {
    a.erase(a.find(x));
}
```

方法一要比方法二快得多，因为方法2需要两次查询

