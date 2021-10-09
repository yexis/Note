###### Json Marshal : 将数据编码成json字符串

```go
type Stu struct {
    Name  string `json:"name"`
    Age   int
    HIgh  bool
    sex   string
    Class *Class `json:"class"`
}

type Class struct {
    Name  string
    Grade int
}

func main() {
    //实例化一个数据结构，用于生成json字符串
    stu := Stu{
        Name: "张三",
        Age:  18,
        HIgh: true,
        sex:  "男",
    }

    //指针变量
    cla := new(Class)
    cla.Name = "1班"
    cla.Grade = 3
    stu.Class=cla

    //Marshal失败时err!=nil
    jsonStu, err := json.Marshal(stu)
    if err != nil {
        fmt.Println("生成json字符串错误")
    }

    //jsonStu是[]byte类型，转化成string类型便于查看
    fmt.Println(string(jsonStu))
}

//{"name":"张三","Age":18,"HIgh":true,"class":{"Name":"1班","Grade":3}}
```

注意：

- 只要是可导出成员，都可以转成json格式。不可导出成员无法转换
- 如果变量打上了json标签，如Name后的`json : name`，那么转化成的json标签就用`name`,否则取变量名作为key
- bool类型也是可以直接转换成json的value值。channel、complex以及函数不能被编码成json字符串。循环的数据结构也不行，会导致marshal陷入死循环
- json编码成字符串后就是纯粹的字符串了

---

###### Json Unmarshal :将json字符串解码到相应的数据结构

```go
type StuRead struct {
    Name  interface{} `json:"name"`
    Age   interface{}
    HIgh  interface{}
    sex   interface{}
    Class interface{} `json:"class"`
    Test  interface{}
}

type Class struct {
    Name  string
    Grade int
}

func main() {
    //json字符中的"引号，需用\进行转义，否则编译出错
    //json字符串沿用上面的结果，但对key进行了大小的修改，并添加了sex数据
    data:="{\"name\":\"张三\",\"Age\":18,\"high\":true,\"sex\":\"男\",\"CLASS\":{\"naME\":\"1班\",\"GradE\":3}}"
    str:=[]byte(data)

    //1.Unmarshal的第一个参数是json字符串，第二个参数是接受json解析的数据结构。
    //第二个参数必须是指针，否则无法接收解析的数据，如stu仍为空对象StuRead{}
    //2.可以直接stu:=new(StuRead),此时的stu自身就是指针
    stu:=StuRead{}
    err:=json.Unmarshal(str,&stu)

    //解析失败会报错，如json字符串格式不对，缺"号，缺}等。
    if err!=nil{
        fmt.Println(err)
    }

    fmt.Println(stu)
}

//{张三 18 true <nil> map[naME:1班 GradE:3] <nil>}
```

注意：

- json字符串解析时，需要一个“接收体”接收解析后的数据，写Unmarshal时必须传递指针。
- 解析时，接收体可自行定义。json串中的key自动在接收体中寻找匹配的项进行赋值。