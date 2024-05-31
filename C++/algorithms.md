##### 1. C++ 去重函数的用法

```cpp
// unique 函数
// 作用：去除容器或者数组中相邻元素的重复出现的元素
// 这里的去除并非真正意义的erase，而是将重复的元素放到容器的末尾，返回值是去重之后的尾地址。
// unique针对的是相邻元素，所以对于顺序顺序错乱的数组成员，或者容器成员，需要先进行排序，可以调用std::sort()函数
sort(nums.begin(), nums.end());
nums.erase(unique(nums.begin(), nums.end()), nums.end());
```

##### 2. 最大公因数   最小公倍数

```cpp
// 计算两个数的最大公因数
gcd(x, y);

// 计算两个数的最小公倍数
lcm(x, y);
```

##### 3. 最大最小值

```cpp
// 两数比较
template <class T> pair <const T&,const T&> minmax (const T& a, const T& b) {
  return (b<a) ? std::make_pair(b,a) : std::make_pair(a,b);
}

// 数组比较
template <class ForwardIterator, class Compare>
  pair<ForwardIterator,ForwardIterator>
    minmax_element (ForwardIterator first, ForwardIterator last, Compare comp);
// 用法
const auto [pmi, pmx] = minmax_element(nums.begin(), nums.end());
int mi = *pmi, ma = *pma;
```

##### 4. 小数 保留两位有效数字  

```cpp
// 自带四舍五入的规则
double to(double v) {
  stringstream ss;
  ss << fixed << setprecision(2) << v;
  return ss.str();
}
```

