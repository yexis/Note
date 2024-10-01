```go
//--------------------------根据层序遍历构件二叉树（完全二叉数）-----------------------------//
func Create(nums []int, index int) *TreeNode{
	if index >= len(nums) {
		return nil
	}
	root := &TreeNode{nums[index], nil, nil}
	if 2 * index + 1 < len(nums) {
		root.Left = Create(nums, 2 * index + 1)
	}
	if 2 * index + 1 < len(nums) {
		root.Right = Create(nums, 2 * index + 2)
	}
	return root
}
//--------------------------------------------------------------------------------------//
```

