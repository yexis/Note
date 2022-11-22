```shell
# -d:   表示以 ":" 分隔
# -f2   表示显示第 2 列
cat rac.log | grep -oP "appid:(\S+)" | cut -d: -f2
```