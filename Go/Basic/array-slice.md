### slice

1. 切片是数组的一个引用，引用类型。自身是结构体，值拷贝传递
2. 长度可以改变。可变数组
3. 遍历方式与数组相同，可使用len计算长度
4. cap可以计算slice最大扩容容量。不能超出数组限制
5. 切片定义： var 变量名 []类型 

> 注意：
>
> 在go语言中，判断一个变量是数组还是切片，就看有没有定义长度

创建切片的各种方式

```go
var s1[] int 
if(s1 == nil) {} else {}
// 使用:=
s2 := []int{}
// 使用make
var s3[] int  = make([]int , 0)
//初始化赋值
var s4[] int = make([]int,0,0)
s5 := []int{1,2,3}
//从数组切片
arr := [5]int {1 ,2, 3, 4, 5}
var s6 []int
s6 = arr[1:4]
```

- make的用法

```go
var slice []type = make([]type, len)
//or
slice := make([]type,len)
//or 
slice := make([]type,len,cap)
```

![image-20200630210400301](/home/yex/.config/Typora/typora-user-images/image-20200630210400301.png)

- append函数

```go
//append函数：在切片的尾部追加元素
var s []string
s = append(s, "a")
fmt.Println(s)
//注意： append函数会 检测 切片是否初始化，如果未初始化，会新建一个
//所以使用append时，切片可以是未初始化的

```

