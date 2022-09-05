

一个动态规划的思想。维护一个一维数组`dp[i]`：表示将数字`i`拆分后所能获得的最大乘积。

那么最后只需要计算`dp[n]`即可。

#### 思路

- 从1开始逐次增加，计算`n=1`,计算`n=2`,计算`n=3`,计算`n=4`......计算`n`。

  这样可以保证，在计算`n`之前，`0 ～ n -1`的结果已经计算出来

- 计算`n`时，分别计算比较`n`从`j（1 <= j < n）`处**拆分成两份和拆分成多份**即`>2`份的情况，取较大值。

  **注意：**`n`拆分成两份的乘积可以使用`i * j`表示。
  
  那么，`n`拆分成多份`>2`的情况的乘积怎么表示，仔细想想，`dp[0] ~ dp[n-1]`均已经计算过，可以使用`j * dp[n-j]`,我们不需要管`n-j`分成了多少份，只需要知道`n-j`拆分后的乘积的最大值是`dp[n-j]`

​       也就是说        ![img](file:////tmp/wps-yex/ksohtml/wpsehx4dM.jpg) 

#### 代码

```go
func max(a,b int) int{
	if a > b {
		return a
	}
	return b
}
func integerBreak(n int) int {
	dp := make([]int,n + 1)
	dp[0], dp[1], dp[2] = 0,0,1
	for i := 3;i <= n;i++ {
		for j := 1; j < i; j++ {
			dp[i] = max(dp[i], max(j* (i - j),j * dp[i - j]))
		}
	}
	return dp[n]
}
```

### 结果

![image-20200730202140953](/home/yex/Desktop/image-20200730202140953.png)