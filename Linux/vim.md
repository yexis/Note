
### vim如何输入tab
``` sh
# 在输入模式下
ctrl+v+i
```

### vim 显示中文乱码
```shell
 set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
 set termencoding=utf-8
 set encoding=utf-8
```

- **命令模式：启动Vim便进入了命令模式**

  常用命令：

  <u>i：切换到输入模式</u>

  <u>x：删除当前光标所在处的字符</u>

  <u>： 切换到底线命令模式</u>

  <u>u:  还原到上一版本,回退(常用)</u>

  <u>ctrl + r: 重复上一动作(常用)</u>

  <u>J : 将光标所在行与下一行合并</u>

  <u>dd : 删除光标所在行整行数据</u>

  <u>x: 向后删除一个元素, X: 向前删除一个元素</u>

  <u>p: 将已复制的数据在光标下一行贴上 , P : 贴在上一行</u>

  <u>G: 移动到档案的最后一行</u>

  <u>gg : 移动到档案的第一行,相当与1G</u>

  <u>n<Enter> 向下移动n行</u>

- **输入模式:命令模式下输入i便进入输入模式**

  字符输入

  ENTER换行

  back space 删除

  del 删除光标后一个字符

  方向键 移动光标

  page up/dowi=上/下翻页

  Insert 切换光标为输入/替换模式

  ESC 退出输入模式

- **底线命令模式:**在命令模式下,按下 : 便进入了底线命令模式

  输入单个或多个字符的命令(可用的命令非常多)

  (省略:)

  q:退出程序

  w:保存文件

  wq:保存并退出

  w!:文件为只读时,强制写入文档

  q!:离开不保存**(!感叹号在vim当中常常具有强制的意思)**

  set nu:显示行号

  set noun:取消行号

  esc 随时可退出底线命令模式

![image-20200630150115789](/home/yex/.config/Typora/typora-user-images/image-20200630150115789.png)

