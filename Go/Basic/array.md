 

在Go语言中，数组和切片是两种不同的数据结构。

**在Go语言中，数组是一种具有相同类型固定大小的数据结构。**

> 注意： 
>
> 1.不同大小的数组之后不能相互赋值，即使类型相同
>
> 2.在Go中，数组属于基本类型，其之间的赋值是值拷贝，非引用拷贝。同样将数组作为参数在函数间传递，也是值拷贝。
>
> 

```go
array4 := [2]int {1,2}
l := len(array4)
fmt.Println(""l)

//访问数组中的元素
array4 := [2]int {1,2}
//访问数组下标为0处的值并打印
fmt.Println(array4[0])

//二维数组
array5 := [2][2]int{{1,2},{3,4}}
for i,tempArray := range array5{
    //此时tempArray是一维数组
    //再通过range 遍历tempArrayy一维数组
    for j,v := range tempArray{
        fmt.Println(j,v)
    }
}
```

