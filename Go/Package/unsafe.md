###### Question：

1. 什么是unsafe.Pointer
2. 什么是unsafe

---

**非类型安全的指针：unsafe.Pointer**

unsafe包用于Go编译器，可以绕过Go语言的类型系统，直接操作内存。

```
type ArbitraryType int

type Pointer *ArbitraryType
```

`Arbitrary` 是任意的意思，也就是说 Pointer 可以指向任意类型，实际上它类似于 C 语言里的 `void*`。

所以：

> 任意类型的指针和unsafe.Pointer可以相互转换
>
> uintptr类型和unsafe.Pointer可以相互转换

```go
func main() {
	s := make([]int, 9, 20)
	var Len = *(*int)(unsafe.Pointer(uintptr(unsafe.Pointer(&s)) + uintptr(8)))
	fmt.Println(Len, len(s)) // 9 9

	var Cap = *(*int)(unsafe.Pointer(uintptr(unsafe.Pointer(&s)) + uintptr(16)))
	fmt.Println(Cap, cap(s)) // 20 20
}
```

