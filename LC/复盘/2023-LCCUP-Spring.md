### T3 

##### 1 emphasis

* 离散化
* 二维差分
* 扫描线

 

##### 2 key points

小扣在探索丛林的过程中，无意间发现了传说中“落寞的黄金之都”。而在这片建筑废墟的地带中，小扣使用探测仪监测到了存在某种带有「祝福」效果的力场。
经过不断的勘测记录，小扣将所有力场的分布都记录了下来。forceField[i] = [x,y,side] 表示第 i 片力场将覆盖以坐标 (x,y) 为中心，边长为 side 的正方形区域。

若任意一点的 力场强度 等于覆盖该点的力场数量，请求出在这片地带中 力场强度 最强处的 力场强度。

注意：

力场范围的边缘同样被力场覆盖。

##### 3 thought

**注意**

**直接遍历所有矩形的顶点是错误的思路**

**二维差分**

* 针对矩形i，将矩形i中的所有点的值+1
* 统计值最大的点
* 区域可能会很大，所有需要将区域离散化

**扫描线?**



##### 4 code

```cpp
// 离散化，直接更新
// 离散化后的数据量不会太大，可以直接暴力
class Solution {
public:
    using ll = long long;
    int fieldOfGreatestBlessing(vector<vector<int>>& nums) {
        int n = nums.size(), m;
        set<ll> sa, sb;
        vector<array<ll, 4> > va;
        for (int i = 0; i < nums.size(); i++) {
            int x = nums[i][0], y = nums[i][1], r = nums[i][2];
            va.push_back({x * 2ll - r, x * 2ll + r + 1, y * 2ll - r, y * 2ll + r + 1});
            sa.insert(x * 2ll - r);
            sa.insert(x * 2ll + r + 1);
            sb.insert(y * 2ll - r);
            sb.insert(y * 2ll + r + 1);
        }

        m = sa.size(), n = sb.size();
        int cnt[m][n];
        memset(cnt, 0, sizeof(cnt));

        vector<ll> vsa(sa.begin(), sa.end()), vsb(sb.begin(), sb.end());
        for (auto& [a, b, c, d] : va) {
            int x1 = lower_bound(vsa.begin(), vsa.end(), a) - vsa.begin();
            int x2 = lower_bound(vsa.begin(), vsa.end(), b) - vsa.begin();
            int y1 = lower_bound(vsb.begin(), vsb.end(), c) - vsb.begin();
            int y2 = lower_bound(vsb.begin(), vsb.end(), d) - vsb.begin();
            for (int i = x1; i < x2; i++) {
                for (int j = y1; j < y2; j++) {
                    cnt[i][j]++;
                }
            }
        }

        int ans = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                ans = max(ans, cnt[i][j]);
            }
        }
        return ans;

    }
};

// 离散化+二维差分
class Solution {
public:
    using ll = long long;
    int fieldOfGreatestBlessing(vector<vector<int>>& nums) {
        int n = nums.size(), m;
        vector<ll> X, Y;
        ll lx, ly, rx, ry;
        for (auto& e : nums) {
            ll x = e[0], y = e[1], r = e[2];
            lx = x * 2 - r;
            rx = x * 2 + r + 1;
            ly = y * 2 - r;
            ry = y * 2 + r + 1;
            X.push_back(lx);
            X.push_back(rx);
            Y.push_back(ly);
            Y.push_back(ry);
        }
        sort(X.begin(), X.end());
        sort(Y.begin(), Y.end());
        X.erase(unique(X.begin(), X.end()), X.end());
        Y.erase(unique(Y.begin(), Y.end()), Y.end());
        m = X.size(), n = Y.size();
        int diff[m + 1][n + 1]; 
        memset(diff, 0, sizeof(diff));
        for (auto& e : nums) {
            ll x = e[0], y = e[1], r = e[2];
            lx = x * 2 - r;
            rx = x * 2 + r + 1;
            ly = y * 2 - r;
            ry = y * 2 + r + 1;
            lx = lower_bound(X.begin(), X.end(), lx) - X.begin();
            rx = lower_bound(X.begin(), X.end(), rx) - X.begin();
            ly = lower_bound(Y.begin(), Y.end(), ly) - Y.begin();
            ry = lower_bound(Y.begin(), Y.end(), ry) - Y.begin();
            diff[lx + 1][ly + 1]++;
            diff[lx + 1][ry + 1]--;
            diff[rx + 1][ly + 1]--;
            diff[rx + 1][ry + 1]++;
        }

        int ans = 0;
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                diff[i][j] += diff[i - 1][j] + diff[i][j - 1] - diff[i - 1][j - 1]; 
                ans = max(ans, diff[i][j]);
            }
        }
        return ans;

    }
};
```



##### 5 summary

###### 5.1 array可以使用结构化绑定，但是vector不行

```cpp
vector<array<int, 3>> v;
for (auto& [a, b, c, d] : v) {

}

```



###### 5.2 离散化常用技巧

* 将所有可能出现的数加入到集合
* 集合排序、去重
* 通过二分计算离散化后的坐标

```cpp
// method1
vector<int> v;
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
int real_x = lower_bound(v.begin(), v.end(), fake_x);


// method2
vector<int> v;
sort(v.begin(), v.end());
int sz = unique(v.begin(), v.end()), v.end();
v.resize(sz);
int real_x = lower_bound(v.begin(), v.end(), fake_x);
```



###### 5.3 二维差分

```cpp
// 将平面二维坐标系中，某个矩形区域的所有点值+1
// 设左下角坐标为(lx, ly), 右上角坐标为(rx, ry)
diff[lx][ly]++;
diff[rx + 1][ry + 1]++;
diff[lx][ry + 1]--;
diff[rx + 1][ly]--;
// 差分后的统计
for (int i = 0; i < m; i++) {
  for (int j = 0; j < n; j++) {
    if (i && j) {
      diff[i][j] += diff[i - 1][j] + diff[i][j - 1] - diff[i - 1][j - 1];
    } else if (i) {
      diff[i][j] += diff[i - 1][j];
    } else if (j) {
      diff[i][j] += diff[i][j - 1]; 
    }
  }
}

// 或者将所有坐标右移一维，留出哨兵位置0
diff[lx + 1][ly + 1]++;
diff[rx + 2][ry + 2]++;
diff[lx + 1][ry + 2]--;
diff[rx + 2][ly + 1]--;
for (int i = 0; i < m; i++) {
  for (int j = 0; j < n; j++) {
    diff[i][j] += diff[i - 1][j] + diff[i][j - 1] - diff[i - 1][j - 1];
  }
}
```

