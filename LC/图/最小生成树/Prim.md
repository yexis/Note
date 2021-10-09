**Prim算法主要思路：**

抽象（假想:假设存在，但不存在）出两个集合，集合`V`  和集合`Vnew`

- 最开始，所以的图节点都在集合V中
- 如果一个节点加入到了最小生成树中，则将该节点加入到Vnew(即Vnew保存的是最小生成树中的节点)



> 说明： Vnew即最小生成树



**Prim算法主要维护三个数组**

- `lowcost []int`，表示V中的节点，离Vnew中所有节点的最短距离。如果节点已经加入到了Vnew中，则置为-1

- `latest []int`，表示V中的节点，离Vnew中最近的节点的下标，如果节点已经加入到了Vnew中，则置为-1

- `v []int`，表示V中节点的访问情况，最开始全部为0,表示未加入到Vnew中，若某节点加入到了V中， 则将其置为-1



**步骤：**

1. 随机选择一个起点，并将其加入到Vnew中。同时，更新此时的lowcost、latest和v
2. 遍历lowcost，寻找lowcost中的最小值min（假设为 j ），将与索引 j 相对应的节点加入到Vnew中，更新lowcost[j]、latest[j]和v[j]
3. 找到最小值 j 后，此时lowcost和latest中的所有节点都要更新，因为此时Vnew节点增加了，V集合中的节点离Vnew集合的距离可能会缩短。
4. 此时已更新完所有的lowcost和latest。
5. 重复步骤2,直到访问了所有的节点

**很明显，最后需要计算的最小生成树各节点之间的距离和便是每一步lowcost中的最小值min的和**



证明：

不会



> 说明：Prim算法是基于顶点来进行查找的，所以与边的多少关系不大，比较适用与比较稠密的图。



golang 代码：

```go
package main

import (
	"fmt"
	"math"
)

var MAXD = math.MaxInt64
//Prim
func Prim(e [][]int, start, n int) int {
	lowcost := make([]int, n) // 与Vnew最近的距离
	latest := make([]int, n)  // 距离Vnew中最近的点
	v := make([]int, n)       // 是否加入到Vnew
	sumD := 0

	for i := 1; i < n; i++ {
		lowcost[i] = e[start][i]
		if e[start][i] < MAXD {
			latest[i] = start
		}else {
			latest[i] = -1
		}
		v[i] = -1
	}

	for i := 1; i < n; i++ {
		var (
			min = MAXD
			k int
		)
		//找到离Vnew集合最近的外点
		for j := 0; j < n; j++ {
			if v[j] == -1 && lowcost[j] < min {
				min = lowcost[j]
				k = j
			}
		}
		//添加最短距离
		sumD += min

		//将k添加到Vnew
		v[k] = 0
		lowcost[k] = -1
		latest[k] = -1

		//更新lowcost
		for j := 0; j < n; j++ {
			if v[j] == -1 && e[k][j] < lowcost[j] {
				lowcost[j] = e[k][j]
				latest[j] = k
			}
		}
	}
	return sumD
}

//将edge转换成邻接矩阵的形式
func extract(edge [][]int, n int) [][]int {
	e := make([][]int, n)
	//初始化
	for i := 0; i < n; i++ {
		e[i] = make([]int, n)
		for j := 0; j < n; j++ {
			e[i][j] = MAXD
		}
	}
	//转化程邻接矩阵
	for i := 0; i < len(edge); i++ {
		e[edge[i][0]][edge[i][1]] = edge[i][2]
		e[edge[i][1]][edge[i][0]] = edge[i][2]
	}
	return e
}
func main() {
    //说明： {0,1,1}表示节点0与节点1的距离为1
	//edge := [][]int{{0,1,4},{1,2,9},{1,3,3},{3,4,4}}
	edge := [][]int{{0,1,10000}}
	e := extract(edge,2)
	res := Prim(e,0,2)
	fmt.Println(res)
}




```



**tips:**

1. 以点为基础，如何保存
   - 邻接矩阵即可
2. V与Vnew如何隔离
   - 使用数组`v`来控制，-1属于v， 0属于Vnew
3. 如何保存V中的点到Vnew集合的最短距离
   - 使用`lowcost`保存V中的点到Vnew的最短距离
4. 距离保存好了，如何保存V中的点到Vnew中最近的点
   - 再新建一个数组`latest`，保存最近的点

**每加入一个点到Vnew中之后，记得更新`lowcost`和`latest`两个数组**