### 添加定时任务
1. 编写脚本 xxx.sh
2. 确定多长时间跑一次脚本
3. 编辑crontab文件，**命令: sudo crontab -e**
4. `*/30 * * * * sh /home/cloud/shell/udw.sh >> /home/cloud/shell/udw.log`
5. 每半小时执行一次  **注意:脚本路径为绝对路径**

udw.sh文件
```sh
# 定时抓取网页数据
q=`date`
echo $q

s=`curl -s -L --max-redirs 5 http://www.xxx.com`
echo "$s" | grep -oP "<td>(\d+)</td>.*<td>(\d+)</td>" | grep -oP "(\d+)" | tail -1
```