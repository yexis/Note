对于自定义的结构体：

```
type TreeNode struct {
	Val int
	Left *TreeNode
	Right *TreeNode
}
```

使用new创建一个对象实例，分配内存

```
root = new(TreeNode)
```



在Go语言中，未进行初始化的变量都会初始化为该类型的零值。

在Go语言中，没有构造函数的概念，对象的创建通常交由一个全局的创建函数来完成。以NewXXX来命令表示构造函数。

```go
func NewTreeNode(val int,L,R *TreeNode) *TreeNode{
    return &TreeNode{val,L,R}
}
```

