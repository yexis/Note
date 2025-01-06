### Title lower_bound详解

##### 1 emphasis

- 二分
- lower_bound



##### 2 key points

`lower_bound`:从左往右找到第一个不满足条件的位置

 

##### 3 thought

使用`lower_bound`时，数组应该条件有序（条件有序，即在条件`condition`下是有序的）。

举个例子，假设数组$a=[a_0,a_1,a_2,...,a_{k-1},a_k,a_{k+1},...,a_n]$

如果$[a_0,a_1,...,a_{k-1}]$满足条件`condition`，而$a_{k},a_{k+1},...,a_{n}$不满足条件`condition`，那么`lower_bound`查询出来的结果便是$k$

依赖区间满足如下形式

$[...,满足,满足,...,满足,|不满足|,不满足,...,不满足]$

```cpp
int kk = lower_bound(a.begin(), a.end(), x, [&](const auto& aa, const auto^ bb) {
  return condition;
});
```





##### 4 code



##### 5 summary

