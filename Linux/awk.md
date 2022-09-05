### 1 基本

```shell
# 格式
$ awk 动作 文件名

# 打印 demo.txt 中的每一行内容
$ awk '{print $0}' demo.txt 

# 打印 i'm yex
echo "i'm yex" | awk '{print $0}'

# awk 根据空格和制表符，将每一行分成若干个字段，依次用 $1,$2,$3 打印出来
# $1: 第一个字段
# $2: 第二个字段
# $3: 第三个字段
$ echo "i'm yex" | awk '{print $2}'

# 默认分隔符是 空格
# 指定分隔符使用 -F 符号
$ awk -F ':' '{print $1}' demo.txt
```



### 2 变量

```shell
# NF: 表示当前行有多少个字段，故 $NF 表示最后一个字段
# 打印 第1个字段 + 倒数第2个字段
$ awk -F ':' '{print $1, $(NF-1)}' demo.txt

# NR: 表示当前处理的是第几行
# 打印 行数 + 每行第一个字段
$ awk -F ':' '{print NR ") " $1}'
```



### 3 函数

```shell
# toupper(): 将字符转换成大写
# 将第一个字段的输出转换成大写
$ awk -F ':' '{print toupper($1) }' demo.txt

# tolower(): 转换成小写
# length: 字符串的长度
# substr: 返回子字符串
# sin(): 正弦
# cos(): 余弦
# sqrt(): 平方根
# rand(): 随机数
```



### 4 条件

```shell
# 输出条件要写在动作的前面
$ awk '条件 动作' demo.txt

# 只输出包含 usr 的行
$ awk -F ':' '/usr/ {print $1}' demo.txt

# 只输出奇数行
$ awk -F ':' 'NR %2 == 1 {print $1}' demo.txt

# 只输出第3行以后的行
$ awk -F ':' 'NR > 3 {print $1}' demo.txt 

# 输出第一个字段等于指定值的行
$ awk -F ':' '$1 == "root" {print $0}' demo.txt
$ awk -F ':' '$1 == "root" || $1 == "bin" {print $0}' demo.txt
```



### 5 if else

```shell
$ awk -F ':' '{if ($1 > "m") print $1}' demo.txt
$ awk -F ':' '{if ($1 > "m") print $1; else print "---"}' demo.txt
```

