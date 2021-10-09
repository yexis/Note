**KrusKal算法的主要思路：**

与Prim算法不同的是，Prim算法是以顶点作为单元来进行考虑，而KrusKal是以边为单位进行考虑。

将邻接矩阵中的所有数据解析成`<m,n,l>`的结构（**这里称为点边式**），其中`m`和`n`分别为两个顶点，` l`为两顶点的权值。将所有点边式按权值大小从小到大依次排序。然后遍历所有的点边式，并做如下判断：

- 如果该点边式的两点`m`、`n`同源（说明`m`和`n`属于同一棵树），跳过
- 如果该点边式的两点不同源，则合并（合并方法比较随便，将`n`的根设为`m`的根即可）



> 至于什么是同源，即两个顶点属于同一个集合（这里的集合即为一颗树，最后所有的数会组成最小生成树/MST）



**Kruskal算法维护两个数据结构**

- `e`: 点边式集合（按权值排序后的）
- `mc`: 并查集，存放各森林，判断两点是否同源。最开始每个点组成一个集合



**步骤:**

1. 初始化：将邻接矩阵转换成点边式，并对点边式进行排序。同时，初始化并查集。
2. 依次遍历所有的点边时，取最小值。
3. 做如下判断：如果该点边式的两个顶点同源，跳过;如果该点边式的两个顶点不同源，则将这两个源（集合）合并
4. 重复步骤2



> 说明：Kruskal算法是基于边来进行查找的，所以与顶点的关系不大，适用于比较稀疏的图。



**golang代码：**

```go
package main

import (
	"fmt"
	"sort"
)

//并查集
var mc []int

//边结构， m,n: 两点  l :长度
type E struct {
	m int
	n int
	l int
}
type ES []E
//实现Interface接口，便于排序
func (es ES) Len() int {
	return len(es)
}
func (es ES) Swap(i, j int) {
	es[i], es[j] = es[j], es[i]
}
func (es ES) Less(i, j int) bool {
	return es[i].l < es[j].l
}

func KrusKal(edge [][]int, n int) int {
	//初始化
	mc = make([]int, n)
	e := Extract(edge)
	sort.Sort(e)
	for i := 0; i < n; i++ {
		mc[i] = i
	}

	//查找
	sumD := 0
	count := 0
	for i := 0; i < len(e); i++ {
		if count == n - 1 {
			break
		}
		r1 := getRoot(e[i].m)
		r2 := getRoot(e[i].n)
		if r1 == r2{
			continue
		}
		//合并
		mc[e[i].m] = r2
		count++
		sumD += e[i].l
	}
	return sumD
}

//寻找根节点
func getRoot(i int) int {
	for mc[i] != i {
		i = mc[i]
	}
	return i
}

//将图解析成 E 的格式
func Extract (edge [][]int) ES {
	e := make([]E, 0)
	for i := 0; i < len(edge); i++ {
		m, n := mm(edge[i][0], edge[i][1])
		et := E{m, n, edge[i][2]}
		e = append(e, et)
	}
	return e
}

func mm(a, b int) (int, int) {
	if a > b {
		return a, b
	}
	return b, a
}
func main() {
     //说明： {0,1,1}表示节点0与节点1的距离为1
	edge := [][]int{{0,1,1}, {0,5,2}, {1,2,3}, {1,3,5}, {1,5,7}, {2,3,1}, {3,4,2}, {4,5,4}}
	//edge := [][]int{{0,1,10000}}
	res := KrusKal(edge,6)
	fmt.Println(res)
}

```





**tips:**

1. 以边为基础，如何保存边?
2. 边需要从小到大排序，需实现按权值排序
   - 使用结构体`<m, n, l>`(起点/终点/权值)
3. 如何把森林中的多个树合并成一棵树?
   -  使用并查集 `mc[] int`



**每加入一条边记得更新并查集，当添加到第`n-1`条边时结束**