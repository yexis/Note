### Title logtrick gcd

##### 1 emphasis

- 最大公约数
- logtrick
- gcd



##### 2 examples



##### 3 key points

- 区间越大，gcd越小（具有单调性，单调递减），且最多减少$log(U)$次，换句话说，$gcd$最多变化$log(U)$次
- 固定左端点时，gcd的值是梯度
- 固定左端点时，相同gcd的值形成的区间右端点一定是连续的，比如 $gcd = [20,20,20,10,10,5,5,5,1]$，所以可以采用二分



##### 4 thought



##### 5 code

```cpp
// 为了方便存储，ans采用的逆序存储，遍历时可以考虑逆序遍历
// ans: 表示以0为左端点，gcd为x时的右端点的起点（gcd为x的右端点会构成一段连续的区间）
vector<pii> cal(vector<int>& nums) {
    vector<pii> ans;
    int n = nums.size();

    for (int i = n - 1; i >= 0; i--) {
        int num = nums[i];
        ans.emplace_back(num, i);

        int k = 0;
        for (int j = 0; j < ans.size(); j++) {
            ans[j].first = gcd(ans[j].first, num);
            if (ans[k].first == ans[j].first) {
                ans[k].second = ans[j].second;
            } else {
                ans[++k] = ans[j];
            }
        }
        ans. resize(k + 1);
    }
  
  	// 常见正序遍历方式
  	int last = n - 1;
  	for (int j = 0; j < ans.size(); j++) {
      	auto [v, pos] = ans[j];
      	// ...
      	// 子数组左端点是0 && 右端点在[pos, last]时，gcd = v
        last = pos - 1;
    }
  
  	// 常见逆序遍历方式
  	int last = n - 1;
  	for (int j = ans.size() - 1; j >= 0; j--) {
      	int nxt = (j + 1 < ans.size() ? ans[j].second : n - 1);
      	auto [v, pos] = ans[j];
      	// ...
      	// 子数组左端点是0 && 右端点在[pos, nxt]时，gcd = v
        last = pos - 1;
    }
  
    return ans;
}
```



##### 6 summary

