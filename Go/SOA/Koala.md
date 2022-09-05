kaola框架代码结构：

```go
.
├── auto_build.sh                           //自动构建脚本
├── bin
│   └── HelloService
├── build.sh                                //编译脚本
├── config
│   ├── scm_config.ini                      
│   ├── scm_config.ini.online.env           //线上配置
│   └── scm_config.ini.test.env             //测试环境配置
├── hello.thrift                            //接口描述文件
├── package.sh                              //编译打包脚本，使用请参考：http://mis.n.mi.com/koala/publish/package.html
├── README
├── run                                     //启动脚本
├── run.sh                                  //带supervise的启动脚本
├── framework                               //框架基础代码（无需更改）
│   └── app
│       ├── app.go
│       ├── config.go
│       ├── generate.go
│       ├── global.go
│       ├── initDb.go
│       ├── initRabbitmq.go
│       ├── initRedis.go
│       ├── initRedisMock.go
│       ├── initSqlMock.go
│       ├── manager.go
│       ├── plugin.go
│       ├── register.go
│       ├── rpc.go
│       └── user_config.go
├── hello                                   //网络相关基础代码，无需更改
│   ├── constants.go
│   ├── helloservice.go
│   ├── helloservice_host_wrapper.go
│   ├── hello_service-remote
│   │   └── hello_service-remote.go
│   ├── helloservice_wrapper.go
│   └── ttypes.go
├── main
│   ├── HelloService.go
│   ├── HelloWorld_handler.go               //HelloWorld的入口文件，需要业务实现
│   ├── hook.go                             //框架各个阶段的回调接口，可以实现自定义功能
│   └── main.go
└── model                                   //业务model层
└── supervise
    └── supervise
└── glide.yaml                              // 依赖说明文件
└── glide.lock                              // 依赖版本锁文件
```

---

- 每次代码修改后必须执行  ./build.sh build_code , 否则会产生glide版本
- 项目依赖的包在go.mod/go.sum中



- 如果要清除缓存，可执行 go clean --modcache
- 然后再执行go mod init 和go mod tidy





- client每次请求服务器返回的数据会被保存（如果代码无改动）

  ![image-20200720113533522](/home/yex/.config/Typora/typora-user-images/image-20200720113533522.png)

需要到后面添加一个-count = 1 

```
go test test/hello_test.go - count = 1
```

