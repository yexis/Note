### Title 2-SAT

##### 1 emphasis

- 2-SAT
- SCC
- 强连通分量



##### 2 examples



##### 3 key points

 

##### 4 thought

###### 4.1 2-SAT中如何判断两种选择是否存在依赖关系

1. 先从$i$出发，对图进行BFS，看能否遍历到$i + n$，结果记作$f_1$

2. 然后从$i + n$出发，对图进行BFS，看能否遍历到$i$，结果记作$f_2$

分情况讨论 ：

- 如果$f1=true, f_2=true$，显然不可能，此时两个点相互依赖，形成矛盾
- 如果$f_1=true, f_2=false$，说明$i$出发能访问到$i+n$，那么应该取$i + n$
- 如果$f_1=false, f_2=true$，说明$i+n$出发能访问到$i$，那么应该取$i$
- 如果$f_1=false, f_2=false$，说明两者没有依赖关系，那么取$i$和$i+n$都可以





##### 5 code



##### 6 summary

