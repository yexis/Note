### 调用关系

client访问kit生成的微服务http server

- 经过transport层的decode获得的http包request数据
- 交给endpoint层处理
- endpoint将request传给业务函数处理，返回response
- 最后一层一层返回给client