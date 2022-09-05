###### golang中接口和面向对象的关系：

golang中没有面向对象，但可以实现面向对象的基础功能，类比如下：

- 基类：golang中的接口相当于面向对象中的基类，（虽然接口并不完全是基类的概念，单在某些情况下，会把接口当作基类来使用），只不过继承基类需要显示，而golang中的接口是隐式，并且传入的方法名和传入传出的参数相同即可。
- 类：golang中的struct相当于面向对象的类。
- 类的属性：golang中的struct的元素相当于面向对象中类的属性。
- 类的方法：golang中struct的方法(也叫method,在某些语言里叫签名函数)相当于面向对象中的类的方法。

---

###### type switch: 对接口变量hole值做类型判断

当 `i`是接口变量的时候，可以用`i.(type)`来对这个接口变量hold的值做类型判断

```go
switch v := i.(type)  {
case int:
    ...
case string:
    ...
default:
    ...
}
```

**注意：之前类型断言是用i.($TYPE)比如i.(int)来判断是不是int类型，但是这里用关键字type。关键字type只能用在switch语句里，如果用在switch外面会报错**。

如：`a := i.(type)`会报错，`use of .(type) outside type switch`

type.switch除了能识别内置类型，也可以识别其他类型，比如函数类型，或带指针的struct。