```go
// 1.Global
// 列出所有的key： 
keys *
// 查看key是什么类型： 
type key_name
//清空所有的key
flushall

// 2.Set
// 返回集合中的所有成员：
SMembers key
// 移除集合中一个或多个成员：  
SRem key member1[merber2...]
// 向集合中添加一个或多个成员
Sadd key member1[member2]

```

