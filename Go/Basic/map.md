### map

- 定义: 

- ```go
  map[keyType]valueType
  //如 
  score := make(map[string]int , 8)
  score["张三"] = 90
  score["小明"] = 100
  //可在声明的同时赋值
  userinfo := map[string]string {
      "name": "yex",
      "password":"123"
  }
  ```

- 判断map中的键是否存在

  ```go
  value,ok := map[key]
  v,ok := score["张三"]
  if(ok) {
      fmt.Println(v)
  } else {
      fmt.Println("error")
  }
  ```

- map的遍历:map中使用for range遍历map

  ```go
  //遍历key , value
  for k ,v :=range score {
      fmt.Println(k,v)
  }
  //仅遍历key
  for k := range score {
      fmt.Println(k)
  }
  ```

- 使用delete()函数删除键值对

  ```
  delete (map,key)//map: 要删除的map ,key: 要删除的键值对的键
  //例子
  delete score(score , "小明")
  
  ```

  