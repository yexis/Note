```go
### 启动workder

1. 开启go module    > export GO111MODULE=on

2. 下载依赖    > go mod download 

3. 配置需要运行docker    > app/worker/workerconfig/task_config.ini        > 例:alone_worker=my_page_info_worker

4. 启动worker    > go run app/worker/main.go
```

