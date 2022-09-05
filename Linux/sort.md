# sort

### 1 工作原理

**sort将文件的每一行作为一个单位，相互比较，比较原则是从首字符向后，依次按ASCII码值进行比较，最后将他们按照升序输出**

**注意：sort 默认是把排序后的结果输出到标准输出**



### 2 详细说明

##### 1）不带参数

```shell
[root]$ cat seq.txt
banana
apple
pear
orange
[root]$ sort seq.txt
apple
banana
orange
pear
```



##### 2）-u 参数	unique

在输出行中去除重复行

```shell
[root]$ cat seq.txt
banana
apple
pear
orange
pear
[root]$ sort seq.txt
apple
banana
orange
pear
pear
[root]$ sort -u seq.txt
apple
banana
orange
pear
# pear 重复被去重了
```



##### 3）-r 参数	reverse

按降序排列

```shell
[root]$ cat number.txt
1
3
5
2
4
[root]$ sort number.txt
1
2
3
4
5
[root]$ sort -r number.txt
5
4
3
2
1
```



##### 4）-o 参数

sort 可以将排序后的结果写入到指定文件，但是无法写入到原文件。如果要把文件按sort排序后写入到原文件，需要使用 -o 参数。

```shell
[root]$ sort -r number.txt > number.txt
[root]$ cat number.txt
[root]$
# 此时的文件 number.txt 被清空了

[root]$ cat number.txt
1
3
5
2
4
[root]$ sort -r number.txt -o number.txt
[root]$ cat number.txt
5
4
3
2
1
# 可以成功写入 原文件
```



##### 5）-n 参数

sort 的排序过程 是将数字按字符来排序，所以排序时可能会将10放在2的前面，-n参数可以使 sort 按照 数值大小进行排序

```shell
[root]$ cat number.txt
1
10
19
11
2
5
[root]$ sort number.txt
1
10
11
19
2
5
[root]$ sort -n number.txt
1
2
5
10
11
19
```



##### 6）-t -k 参数

* -t 参数可以在后面设定间隔符，然后按照某一列进行排序；

* -k 指定间隔符之后，指定按哪列进行排序

```shell
[root]$ cat facebook.txt
banana:30:5.5
apple:10:2.5
pear:90:2.3
orange:20:3.4

[root]$ sort -n -t : -k 2 facebook.txt
apple:10:2.5
orange:20:3.4
banana:30:5.5
pear:90:2.3
# 从结果中可以看出，每一行是按照第二列的值来排序的
```



* 按照多列进行排序

```shell
[root] $ cat facebook.txt
google 110 5000
baidu 100 5000
guge 50 3000
sohu 100 4500

# 以空格符分隔，然后按照第 1 列排序
[root]$ sort -t ‘ ‘ -k 1 facebook.txt
baidu 100 5000
google 110 5000
guge 50 3000
sohu 100 4500

# 以空格符分隔，然后按照第 2 列排序
$ sort -n -t ‘ ‘ -k 2 facebook.txt
guge 50 3000
baidu 100 5000
sohu 100 4500
google 110 5000
```



##### 7）-f 参数

将小写字母转换成大写字母进行比较，忽略大小写



##### 8）-c 参数

检查文件是否已排序好，如果乱序，则输出第一个乱序行的相关信息，最后返回1



##### 9）-b 参数

忽略每一行前面所有的空白部分，从第一个可见字符开始比较

