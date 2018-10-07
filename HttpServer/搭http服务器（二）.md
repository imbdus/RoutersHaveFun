---

title:搭http服务器（二） 配置树莓派

---

连接好树莓派以后,有两种方式操作树莓派
一种是给树莓派插上HDMI显示器,连上键盘鼠标
另一种是给树莓派插上网线,通过ssh访问

前者只要会用linux就好办,我们推荐后者,方便快捷.

具体的教程出门搜索可以搜到一大堆,我们不多浪费口水,只说一下大致的流程.

# 大致流程

这一段讲 从拿到树莓派 到 可以使用另一台win设备ssh树莓派的过程. 如果你的树莓派配置好了 跳过.

## 材料
至少需要的材料:

树莓派的 SD卡\电源
路由\网线
一台win-PC 读卡器

软件:
putty或其他ssh软件
ImageWriter([Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/))
raspbian 镜像 (官网下载)
config.txt文件(有的树莓派需要这个,所以格式化前先备份之)

## 流程

* 下载Win32 Disk Imager 和raspbian 镜像文件
* 写入镜像到SD卡
* 替换config.txt文件(有的树莓派需要这个)
* 并新建一个空的文件 文件名为ssh 没有任何后缀
* SD卡插入树莓派,插好网线,上电

* 路由器设置页面找到树莓派的地址
* 设置树莓派为固定IP (MAC地址绑定)
* ssh进入
* 初始 user:pi password: raspberry
* 对树莓派进行初始操作sudo raspi-config
* 最主要的初始操作是扩充文件系统(Expand Filesystem) 这个很关键

之后是一些软件的常规操作 和linux系统下几乎没有差别

* 改初始密码 或者改用户\配置不验证密码等等
* 核查自己的发行版 换相应的linux软件源 这一步很关键 换错了会出现很多问题
* update.安装shell下常用的软件 vim, screen, tightvncserver, samba等等


网上有详细的教程教这一段,应有尽有,关键词:配置树莓派

# 然后

有一些别的相对好玩的初始化操作

* 给tightvncserver写[开机启动脚本](http://bdus-00.farbox.com/post/shu-mei-pai-da-wang-zhan/2016-02-08)
* 让树莓派[开机发送邮件](http://bdus-00.farbox.com/post/shu-mei-pai-da-wang-zhan/2016-02-06)

* 配置树莓派wifi连接