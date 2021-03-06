- 强制删除带锁文件： 


```
sudo rm -rf 文件名
```

- 安装软件

```
sudo apt-get install ...
//如
sudo apt-get install go
```

- 强制关闭软件

```go
//获取进程pid
ps -u 用户名
//杀死pid
kill -9 pid
```

- 查看端口占用情况

```go
//查看已经连接的服务端口
netstat -a
//查看所有的服务端口
netstat -ap
//查看 8080端口
netstat -ap |grep 8080
//查看888端口
lsof -i:8888
```





- **Ctrl L** ：清屏
- **Ctrl C** : 中断正在当前正在执行的程序
- **Ctrl R**，再按历史命令中出现过的字符串：按字符串寻找历史命令（重度推荐）
- **Ctrl A** ： 移动光标到命令行首
- **Ctrl E** : 移动光标到命令行尾
- **Ctrl U** : 清空当前键入的命令
- **Ctrl H** : 删除光标的前一个字符
- **Ctrl D** : 删除当前光标所在字符
- **Ctrl K** ：删除光标之后所有字符







（1）Tab，补充命令

不用输入完整的命令，输入命令前几个字母后Tab键，会自动补全命令提示。

（2）移动光标命令

**Ctrl+A：移动光标到开头**

**Ctrl+E：移动光标到结尾**

Ctrl+F：往光标后面移动一个字符

Ctrl+B：往光标前面移动一个字符

（3）剪切字符

Ctrl+K：剪切光标处到行尾的字符

Ctrl+U：剪切光标处到行首的字符

Ctrl+Y：将剪切的字符进行粘贴

（4）复制粘贴

Ctrl+Ins：复制

Shift+Ins：粘贴

（5）中断正在运行的命令行

Ctrl+C

（6）退出当前Xshell

Ctrl+D

（7）搜索命令行使用过的历史命令记录

Ctrl+R

![img](https://img2018.cnblogs.com/blog/1486105/201906/1486105-20190607162237391-908991423.png)

（8）获取上一条命令的最后部分，用空格分隔开来的部分

ESC+.

![img](https://img2018.cnblogs.com/blog/1486105/201906/1486105-20190607162514973-1746142012.png)

（9）清屏命令

**Ctrl+L**

![img](https://img2018.cnblogs.com/blog/1486105/201906/1486105-20190607162600112-49042492.png)

（10）暂停命令

Ctrl+Z

![img](https://img2018.cnblogs.com/blog/1486105/201906/1486105-20190607162736245-1238925794.png)

如上面sleep 40命令执行后一直动不了，光标一直在闪，按Ctrl+Z后就可以重新回到命令行

（11）锁屏

Ctrl+S

（12）解除锁屏

Ctrl+Q

（13） ！+命令 执行上一条命令，!! 执行上两条命令