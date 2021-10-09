deepin 20下安装mysql

 在deepin系统中一般是使用默认的官方apt源，但是官方的源无法安装mysql-server。

自己折腾了

所以最好的办法是切换掉官方的源，使用其他的源来安装。

- deepin 20 beta

  1.替换源需要修改文件夹etc/apt中的souces.list文件。以防万一，可以先将该文件备份到其他地方。

  ```go
  //可以先切换到/etc/apt目录下
  cd /etc/apt
  //拷贝sources.list文件 cp -r 源文件 目的地
  cp -r sources.list /home
  ```

  修改文件sources.list中的内容，并保存

  ```go
  //打开sources.list文件;注意：sources.list文件是系统文件，必须使用管理员权限修改
  sudo vim sources.list
  //将sources.list中的全部注释掉
  //在最后一行添加
  deb [by-hash=force] https://mirrors.tuna.tsinghua.edu.cn/deepin panda main contrib non-free
  ```

  ![image-20200720163430207](/home/yex/Pictures/blog/20200720/image-20200720163430207.png)

  sources.list文件改后的内容

  ![image-20200720163500334](/home/yex/Pictures/blog/20200720/image-20200720163500334.png)

  最后执行以下命令，更新(在任何目录下都可以)

  ```go
  sudo apt-get update
  sudo apt-get upgrade
  ```

  开始安装mysql

  ```go
  sudo apt-get install sql-server
  sudo apt-get install sql-client
  ```

  ![image-20200720163749999](/home/yex/Pictures/blog/20200720/image-20200720163749999.png)

  ![image-20200720163841283](/home/yex/Pictures/blog/20200720/image-20200720163841283.png)

  查看版本

  ```
  mysql --version
  ```

  ![image-20200720163915423](/home/yex/Pictures/blog/20200720/image-20200720163915423.png)