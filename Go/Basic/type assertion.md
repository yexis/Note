type assertions:

限制： 只能用于interface的值

```go
//假如 i 是一个interface类型;
t := i.(T) 
```

断言interface i的值类型是T，如果是，t则为interface i的值，并且t的类型也为interface i的值类型；如果不是，则panic报错，类似：panic: interface conversion: interface {} is int, not float64

```
t, ok := i.(T)
```

如果是，t则为interface i的值，并且t的类型也是interface i的值类型，ok为布尔类型true; 

**如果不是，不会报panic，而是ok为false，同时t的值类型为T，t的值为T类型的初始值。**



**类型断言是个好东西，可以将空接口变量hold的变量重新赋值给非空接口。**