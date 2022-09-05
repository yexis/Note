### nil是什么？

> 在Go中，nil是预定义的标识符，代表指针、通道、函数、接口、映射或切片的零值，即一个预定义好的变量

```go
if err != nil {
    // do something
}
```

> 当出现不等于nil时，说明出现了某些错误，需要进行错误处理。
>
> 如果不等于nil,说明运行正常。

在Go语言中，如果声明了一个变量但没有进行赋值，那么这个变量就是该类型的默认零值

```
bool false
numbers 0
string ""
pointers nil
slices nil
maps nil
channels nil
functions nil
interface nil
```

当定义了一个struct,

```go
type person struct {
    Age int
    Name string
    Friends []person
}
var p Person // p{0,"",nil}
```

