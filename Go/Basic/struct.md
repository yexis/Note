###### **Golang结构体**

golang是通过结构体和相关属性来定义类和属性的。**注意：属性的首字母大小写的意义是不同的**

> golang根据首字母的大小来确定可以访问的权限。无论是方法名、常量名、变量名、还是结构体的名称，如果手写字母大写，是可以被其他包访问的。如果首字母小写，则只能在本包中使用。
>
> **可以简单理解成，大写字母是公有的(public)，小写字母是私有的（private）**
>
> **重点：类属性如果是小写字母开头，则其序列化会丢失属性对应的值，同时也无法进行json解析**



**导出的标识符：**

1. 标识符的首字母Unicode大写字母（Unicode "Lu"类）
2. 标识符要在包块中进行的声明，或它是个字段名/方法名

其他所有的标识符都不是导出的。