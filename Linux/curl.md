### curl命令不支持301、302的解决办法
```sh
# -L 指令其实就是支持 location 头
# --max-redirs 就是支持的重定向深度，这里指定 5 主要原因是方式进入死循环
curl -L --max-redirs 5 http://www.reqbin.com/echo
```

### curl -s 解决进度打印问题
```sh
# -s 不打印进度信息
curl -s www.baidu.com
```