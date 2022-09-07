```sh
//查看已经连接的服务端口
netstat -a
//查看所有的服务端口
netstat -ap
//查看 8080端口
netstat -ap | grep 8080
netstat -nlp | grep 8080
```