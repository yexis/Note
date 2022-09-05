gopath的包管理：

先说说还没有go mod的时候，它是这么找的：

1. 项目根目录下有vendor，那就在vendor下找；
2. vendor找不到包就往GOROOT、$GOPATH找；
3. 以上都找不到，就去网上找，比如"github.com/TomatoMr/something"，那就去GitHub上找。





go mod 来了：

```go
go mod init <module_name>  // 对一个项目的module进行初始化，module_name是选填的，可以在初始化的时候就制定module名
$ go mod tidy // 添加包，清除没有引用的包
```

  执行完两句命令之后，可以看到，项目下多了两个go.mod和go.sum文件

go.mod： 可以理解为包管理文件

go.sum: 可以理解为包的版本控制文件



那么go mod 如何做到版本控制？

> 先将go mod中的文件版本号改为 v0.0.1 ，然后在项目下执行go mod tidy ，可以看到go.sum中的文件也相应地改变了





---

掌握go mod的一些知识

###### 需要学习什么是go mod

```
Usaget: 
go mod <command> [arguments]
The Commands are: 
	downloads	download modules to local cache # 下载包
 	edit		edit go.mod from tools or scripts# 修改或替换包
	graph   	print module requirement graph # 打印依赖关系
	init		initialize new module in current directory# 初始化项目
	tidy		add missing and remove unused modules# 更新依赖包
	vendor      make vendored copy of dependencies # 复制依赖包到vendor
    verify      verify dependencies have expected content # 检查依赖是否正确
    why         explain why packages or modules are needed # 解释为什么需要包或模块，应该说是那些模块会调用到那些依赖包和模块
```

###### 环境变量

```go
GO111MODULE=
# 默认当项目中有go.mod文件就用go mod管理，没有就还是旧的方式，GO111MODULE=on强制使用go mod，GO111MODULE=off关闭go mod
GOPROXY="https://goproxy.cn,https://proxy.golang.org,https://goproxy.io,direct"
# GOPROXY设置代理，我建议设置成与我上面一致，第一个地址是七牛云的代理，防止被墙，direct指的是从源站下载
GOSUMDB="sum.golang.org"
# 指示校验和服务器的地址和公钥，这里指"sum.golang.org"的公钥可省略，若要关闭校验，GOSUMDB=off。
GOPRIVATE=""
# GOPRIVATE表示私有仓库。私有仓库下的所有依赖一律从源站下载，而且不做校验。
GONOPROXY=""
# 与GOPROXY对应
GONOSUMDB=""
# 与GOSUMDB对应
```

###### 清除mod下载的包

```
go clean -modcache
```

