
1. C++ 去重函数的用法
```cpp
// unique 函数
// 作用：去除容器或者数组中相邻元素的重复出现的元素
// 这里的去除并非真正意义的erase，而是将重复的元素放到容器的末尾，返回值是去重之后的尾地址。
// unique针对的是相邻元素，所以对于顺序顺序错乱的数组成员，或者容器成员，需要先进行排序，可以调用std::sort()函数
sort(nums.begin(), nums.end());
nums.erase(unique(nums.begin(), nums.end()), nums.end());
```



2. 最大公因数   最小公倍数

   ```cpp
   // 计算两个数的最大公因数
   gcd(x, y);
   
   // 计算两个数的最小公倍数
   lcm(x, y);
   ```

   