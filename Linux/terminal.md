- **cd命令**

  说明：change directory的缩写，cd命令后跟一个路径，用于切换当前用户所在的路径，可以是绝对路径和相对路径

  例子：

  ```
  cd /system/bin 表示切换到/system/bin路径下
  cd logs 表示切换到logs路径
  cd / 表示切换到根目录路径
  cd ../ 表示切换到上一层路径
  ```

- **ls命令**

  格式： ls<参数><路径>

  说明：list的缩写，后可跟一个路径或参数，也可以不跟。表示列举当前路径或当前目录下的所有文件信息。最常用的参数是 -l ，即命令 ls -l

  例子：

  ```
  ls / 显示根目录下的所有文件和文件夹
  ls -l/data 显示/data路径下的所有文件及文件夹的详细信息
  ls -l 显示当前路径下的所有文件及文件夹的详细信息
  ls *l wc 显示当前目录下面的文件数量
  ```

- **cat命令**

  格式：cat <文件>

  说明：cat是concatenate的缩写，表示读取文件内容及拼接文件

  例子:

  ```
  cat /sys/devices/system/cpu/online 读取 /sys/devices/system/cpu路径下online文件的内容
  cat test.txt 读取当前路径下test.txt文件内容
  ```

- **rm命令**

  格式：rm<文件> 或 rm -r <文件夹>

  意义： remove的缩写。用于删除文件或文件夹，常用参数-r，-f。 -r:删除目录，也可以删除文件。-f:表示强制删除，不需要确认。

  例子：

  ```
  rm -f path 删除path
  rm test.txt 删除test.txt
  ```

- **mkdir命令**

  格式：*mkdir 文件夹*

  意义：mkdir 是make directory的缩写，用于创建文件夹。创建文件夹前需保证当前用户对当前路径的有修改的权限。

  例子：

  ```
  mkdir /data/path 在/data路径下创建path文件夹
  mkdir -p a/b/c 参数 -p 表示创建多级文件夹，表示在当前路径下创建文件夹a,a包含文件夹b,b包含文件夹c
  ```

- **touch命令**

  格式：*touch [选项] 文件名*

  说明： 创建文件

  例子： 

  ```
  touch yex.txt
  ```

- **cp命令**

  格式：cp<文件><目标文件> 或者cp -r <文件夹> <目标文件夹>

  说明：copy的缩写，用于复制文件和文件夹

  例子：

  ```
  cp /data/logs /data/local/tmp/logs 复制/data路径下的logs到/data/local/tmp路径下
  cp 1.sh /sdcard/ 复制当前路径下的1.sh到/sdcard下。
  ```

- **kill命令**

  格式： kill PID码

  说明： 结束当前进程。先通过输入命令 ps au查看进程，找到需要终止进程的PID再通过kill PID即可，如我这里想要终止的进程是vim test.py，查到的PID是3163，我们可以输入kill 3163结束这个程序，如果结束不了，可以通过kill -9 PID码强制结束，即kii -9 3163



- **grep命令**

   格式：grep "被查找的字符串" 文件名

  说明：从文件内容查找匹配指定字符串的行

  ```go
  //从文件内容查找与正则表达式匹配的行：
  grep –e "正则表达式" 文件名
  
  
  //查找时不区分大小写：
  grep –i "被查找的字符串" 文件名
  
  //查找匹配的行数：
  grep -c "被查找的字符串" 文件名
  
  //从文件内容查找不匹配指定字符串的行：
  grep –v "被查找的字符串" 文件名
  
  
  //常用查找字符串
  grep -r "被查找字符串" 文件夹/文件名
  
  
  ```
  
  

* **wc命令**

  ```shell
  wc -l filename 就是查看文件里有多少行
  wc -w filename 看文件里有多少个word。
  wc -L filename 文件里最长的那一行是多少个字。
  ```

  