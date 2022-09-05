```golang
//将字符串s中前缀字符串prefix去掉返回
func TrimPrefix(s, prefix string) string


//将字符串s中后缀字符串prefix去掉返回
func TrimSuffix(s, suffix string) string

// 字符串替换 
// strings.Replace 函数使用参数 n 指定替换次数，当 n 小于 0 表示全部替换。
strings.Replace(s, old, new string, n int) string
// strings.ReplaceAll 函数匹配之后全部替换
strings.ReplaceAll(s, old, new string) string

```

